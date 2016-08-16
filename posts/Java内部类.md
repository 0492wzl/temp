---
title: Java内部类
date: 2016-08-16 16:31:09
categories: JAVA
tags: 内部类

---

内部类是指在一个外部类的内部再定义一个类。类名不需要和文件夹相同。

内部类分为： **成员内部类**、**局部内部类** 、 **静态嵌套类** 、**匿名内部类** 。

## 成员内部类
成员内部类是最普通的内部类，它的定义为位于另一个类的内部，形如下面的形式：
<pre>
	class Outter  {
		private int age = 12;
		
		class Inner{
			private int age = 13;
			public void print(){
				int age = 14;
				System.out.println("局部变量：" + age);
				System.out.println("内部类变量：" + this.age);
				System.out.println("外部类变量：" + Outter.this.age);
			}
		}
	}
</pre>
<pre>
	public static void main(String[] args) {
		Outter out = new Outter();
        Outter.Inner in = out.new Inner();
        in.print();
	}
</pre>
运行结果：
<pre>
	局部变量：14
	内部类变量：13
	外部类变量：12
</pre>
从本例可以看出：成员内部类，就是作为外部类的成员，**可以直接使用外部类的所有成员和方法，即使是private的**。虽然**成员内部类可以无条件地访问外部类的成员，而外部类想访问成员内部类的成员却不是这么随心所欲了**。在外部类中如果要访问成员内部类的成员，必须**先创建一个成员内部类的对象，再通过指向这个对象的引用来访问**：
<pre>
	class Outter  {
		private int age = 12;
		
		public Outter(int age){
				this.age = age;
				getInstance().print();//必须先创建成员内部类的对象，再进行访问
		}
		
		private Inner getInstance(){
			return new Inner();
		}
		
		class Inner{
			public void print(){
				System.out.println("内部类没同名，所以直接调用外部类成员变量：" + age);
			}
		}
	}
</pre>
<pre>
	public static void main(String[] args) {
        Outter out = new Outter(10);
    }
</pre>
运行结果：
<pre>
	内部类没同名，所以直接调用外部类成员变量：10
</pre>
内部类可以拥有 private访问权限、protected访问权限、public访问权限及包访问权限 。
比如上面的例子，如果成员内部类Inner用private修饰，则只能在外部类的内部访问，如果用public修饰，则任何地方都能访问，如果用protected修饰，则只能在同一个包下或者继承外部类的情况下访问，如果是默认访问权限，则只能在同一个包下访问。
这点和外部类有点不一样，外部类只能被public和包访问两种权限修饰。
由于成员内部类看起来像是外部类的一个成员，所有像类的成员一样拥有多种修饰权限。要注意的是，成员内部类布恩那个包含static的变量和方法。因为成员内部类需要先创建外部类，才能创建它自己的。

----------

## 局部内部类
局部内部类是定义在一个方法或者一个作用域里面的类，它和成员内部类的区别在于局部内部类的访问仅限于方法内或者该作用域内。
<pre>
	class Outter  {
		private static int age = 12;
		
		public void Print(final int x){//这里局部变量x必须设置为final类型！
			class Inner{
				public void inPrint(){
					System.out.println(x);
	                System.out.println(age);
				}
			}
			new Inner().inPrint();
		}
	}
</pre>
<pre>
	 public static void main(String[] args) {
		 Outter out = new Outter();
	        out.Print(10);
    }
</pre>
运行结果：
<pre>
	10
	12
</pre>
本例中我们将内部类移到了外部类的方法中，然后在外部类的方法中再生成一个内部类对象去调用内部类的方法。如果此时我们需要往外部类的方法中传入参数，那么外部类的方法形参必须使用**final**定义。
换言之，在方法中定义的内部类只能访问方法中final类型的局部变量，这是因为在方法中定义的局部变量相当于一个常量，它的生命周期超出方法运行的生命周期，由于局部变量被设置为final,所以不能在内部类中改变局部变量的值。

----------

## 静态嵌套类
又叫静态布局类、嵌套内部类，就是修饰为static的内部类。声明为static的内部类，不需要内部类对象和外部类对象之间的联系，就是说我们可以直接引用outer.inner，即不需要创建外部类，也不需要创建内部类。
<pre>
	class Outter  {
		private static int age = 12;
	
		static class Inner {
			public void print(){
				System.out.println(age);
			}
		}
		
	}
</pre>
<pre>
	 public static void main(String[] args) {
		Outter.Inner in = new Outter.Inner();
		in.print();
    }
</pre>
运行结果：
<pre>
	12
</pre>
可以看到，如果用static将内部类静态化，那么内部类就只能反问外部类的静态成员变量，具有局限性。
其次，因为内部类被静态化，因此Outter.Inner可以当作一个整体看，可以直接new出内部类的对象（**通过类名访问static，生不生成外部类对象都没关系**）。

----------

## 匿名内部类
匿名内部类应该是平时我们编写代码使用最多的，在编写事件监听的代码时使用匿名内部类不但方便，而且使代码更加容易维护。下面这段代码是Android事件监听代码：
<pre>
	scan_bt.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                // TODO Auto-generated method stub

            }
        });

    history_bt.setOnClickListener(new OnClickListener() {       
        @Override
        public void onClick(View v) {
            // TODO Auto-generated method stub

        }
    });
</pre>
这段代码为两个按钮设置监听器，这里面就使用了匿名内部类。具体位置是这段：
<pre>
	new OnClickListener() {   
        @Override
        public void onClick(View v) {
            // TODO Auto-generated method stub

        }
    }
</pre>
代码中需要给按钮设置监听器对象，使用匿名内部类能够在父类或者接口中的方法情况下同时产生一个相应的对象，但是前提是这个父类或者接口必须先存在，才能这样使用。当然像下面这种写法也是可以的，跟上面匿名内部类大道的效果相同：
<pre>
	private void setListener()
	{
	    scan_bt.setOnClickListener(new Listener1());       
	    history_bt.setOnClickListener(new Listener2());
	}
	
	class Listener1 implements View.OnClickListener{
	    @Override
	    public void onClick(View v) {
	    // TODO Auto-generated method stub
	
	    }
	}
	
	class Listener2 implements View.OnClickListener{
	    @Override
	    public void onClick(View v) {
	    // TODO Auto-generated method stub
	
	    }
	}
</pre>
这种写法虽然能达到一样的效果，但是即冗长又难以维护，所以一般使用匿名内部类的方法来编写事件监听代码。同样的，匿名内部类也是不能有访问修饰符和static修饰符的。
**匿名内部类是唯一一种没有构造器的类**。正因为其没有构造器，所以匿名内部类的使用范围非常有限，**大部分匿名内部类用于借口回调**。匿名内部类在编译的时候由系统自动起名为Outter$1.class。一般来说，匿名内部类用于继承其他类或是实现接口，并不需要增加额外的方法，只是对继承方法的实现或是重写。