---
title: 全面解析Java注解
date: 2016-08-15 13:31:05
categories: JAVA
tags: 注解

---

## 概念
Java提供了一种原程序中的元素关联任何信息和任何元数据的途径和方法。自JDK1.5启用。

## 注解的分类
### 按照运行机制分
- 源码注解:注解只在源码中存在，编译成.class文件就不存在了
- 源码注解:注解在源码和.class文件中都存在。如@Override,@Deprecated,@Suppvisewarnings
- 运行时注解:在运行阶段还起作用，甚至会影响运行逻辑的注解。如@Autowired

<!--more-->

### 按照来源分
- 来自JDK的注解
- 来自第三方的注解
- 自定义注解
- 元注解，注解的注解 

## 自定义注解 

<pre>@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
public @interface Description {
　　String desc();
　　String author();
　　int age() default 18;
}  
</pre>

### 语法要求
- 使用@interface关键字定义注解
- 成员以无参数无异常方式申明
- 可以用 default 为成员指定一个默认值
- 成员的类型是受限制的，合法的类型包括原始类型及String,Class,Annotation,Enumeration
- 如果注解只有一个成员，则成员名必须取名为value(),在使用时可以忽略成员名和赋值号(=)
- 注解类可以没有成员，没有成员的注解成为标识注解

### 元注解
- @Target 作用域
  - ElementType.CONSTRUCTOR:构造方法声明
  - ElementType.FIELD:字段声明
  - ElementType.LOCAL_VARIVABLE:局部变量声明
  - ElementType.METHOD:方法声明
  - ElementType.PACKAGE:包声明
  - ElementType.PARAMETER:参数声明
  - ElementType.TYPE:类，接口
- @Retention 生命周期
  - RetentionPolicy.SOURCE 只在源码显示，编译时会丢弃
  - RetentionPolicy.CLASS 编译时会记录到class中，运行时忽略
  - RetentionPolicy.RUNTIME 运行时存在，可以通过反射读取
- @Inherited 标识性元注解，允许子类继承，只允许类的继承，接口或者类中的方法不会继承。
- @Documented 生产javadoc时会包含注解信息

### 使用自定义注解
@<注解名>(<成员名1>=<成员值1>,<成员名2>=<成员值2>,...)
<pre>@Description(desc="this is a desc",author="zhangsan",age=20)
public String test(){
　　return "";
}
</pre>

### 解析注解
通过反射获取类、函数或成员上的**运行时**注解信息，从而实现动态控制程序运行的逻辑。
<pre>//1.使用类加载器加载类
Class c = Class.forName("对应类名");
//2.找到类上面的注解
boolean isExist = c.isAnnotationPresent(Description.class);
if(isExist){
　　//3.拿到注解实例
　　Description d = (Description)c.getAnnotation(Description.class);
　　//4.获取注解值
　　d.desc();
　　d.author();
　　d.age();
}
//5.找到方法上面的注解
Method[] ms = c.getMethods();
//(1)
for(Method m : ms){
　　boolean b = m.isAnnotationPresent(Description.class);
　　if(b){
　　　　Description d = (Description)m.getAnnotation(Description.class);
　　　　//...
　　}
}
//(2)
for(Method m : ms){
　　Annotation[] ann = m.getAnnotations();
　　for(Annotation a : ann){
　　　　if(a instanceof Description){
　　　　　　Description d = (Description)a;
　　　　　　//...
　　　　}
　　}
}
</pre>

## 自定义注解实例
### 需求
打印出查询执行的sql
<pre>
	public static void main(String[] args) {
		User u1 = new User();
		u1.setId(10);//查询id=10的user
		
		User u2 = new User();
		u2.setId(12);
		u2.setUsername("zhangsan");
		u2.setPassword("aabbcc");
		u2.setAge(25);
		
		User u3 = new User();
		u3.setUsername("zhangsan,lisi");//查询username = zhangsan或username = lisi的user
		
		String sql1 = query(u1);
		String sql2 = query(u2);
		String sql3 = query(u3);
		
		System.out.println(sql1);
		System.out.println(sql2);
		System.out.println(sql3);
	}
</pre>

### 方法

- 将实体类字段名与数据库列名对应起来
  <pre>
	@Table("user")
	public class User {
	
		@Column("id")
		private int id;
		
		@Column("username")
		private String username;
		
		@Column("password")
		private String password;
		
		@Column("age")
		private int age;
	
		//get...
		//set...
	}
  </pre>

- 创建相应的注解@Table,@Column
	<pre>
	@Target({ElementType.TYPE})
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Table {
	　　String value();
	}
	</pre>
	<pre>
	@Target({ElementType.FIELD})
	@Retention(RetentionPolicy.RUNTIME)
	public @interface Column {
	　　String value();
	}
	</pre>

- 定义主方法query()
	<pre>
	private static String query(User user) {
		StringBuffer sb = new StringBuffer();		
		//1.获取class
		Class c = user.getClass();
		//2.获取table名字
		boolean tableExist = c.isAnnotationPresent(Table.class);
		if(!tableExist){
			return null;
		}
		
		Table t = (Table)c.getAnnotation(Table.class);
		String tableName = t.value();
		sb.append("select * from ").append(tableName).append(" where 1=1 ");
		//3.遍历所有字段
		Field[] fArray = c.getDeclaredFields();
		for(Field field : fArray){
			//4.处理每个字段对应的sql
			//4.1 拿到字段名columnName
			boolean fieldExist = field.isAnnotationPresent(Column.class);
			if(!fieldExist){
				continue;
			}
			Column column = field.getAnnotation(Column.class);
			String columnName = column.value();
			//4.2 拿到字段值columnValue
			String fieldName = field.getName();//拼接get方法，字段第一个字母大写
			String getMethodName = "get" + fieldName.substring(0,1).toUpperCase() + fieldName.substring(1);
			
			Object columnValue =  null;
			try {
				Method getMethod = c.getMethod(getMethodName);//java反射
				columnValue = getMethod.invoke(user);
			} catch (Exception e) {
				e.printStackTrace();
			}
			
			//4.3 拼装sql
			//字段值为0或null不做处理
			if(columnValue == null || (columnValue instanceof Integer && (Integer)columnValue == 0)){
				continue;
			}
			
			sb.append(" and ").append(columnName);
			
			if(columnValue instanceof String){//sql中字符串需要加上单引号
				if(((String) columnValue).contains(",")){
					String[] values = ((String) columnValue).split(",");
					sb.append(" in(");
					for(String v : values){
						sb.append("'").append(v).append("'").append(",");
					}
					sb.deleteCharAt(sb.length()-1);//删除最后一个逗号
					sb.append(")");
				}else{
					sb.append("=").append("'").append(columnValue).append("'");
				}
			}else if(columnValue instanceof Integer){
				sb.append("=").append(columnValue);
			}
		}
		sb.append(";");
		return sb.toString();
	}
	</pre>  

### 输出结果
<pre>
select * from user where 1=1  and id=10;
select * from user where 1=1  and id=12 and username='zhangsan' and password='aabbcc' and age=25;
select * from user where 1=1  and username in('zhangsan','lisi');
</pre>