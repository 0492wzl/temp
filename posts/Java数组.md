---
title: Java数组
date: 2016-05-16 11:01:49
categories: JAVA
tags: 数组

---

## 定义数组

<pre>
	String[] aArray = new String[5];
	String[] bArray = {"a","b","c","d","e"};
	String[] cArray = new String[]{"a","b","c","d","e"};	
</pre>

第一种是定义了一个数组，并且指定了数组的长度，我们这里称它为动态定义。
第二种和第三种在分配内存空间的同时还初始化了值。

<!--more-->

----------  

## 打印数组中元素的值

<pre>
	int[] intArray = {1,2,3,4,5};
	String intArrayString = Arrays.toString(intArray);
	
	// print directly will print reference value
	System.out.println(intArray);//[I@1e081c5

	System.out.println(intArrayString);//[1, 2, 3, 4, 5]
</pre>

重点说明了Java中数组的引用和值的区别，第三行直接打印intArray,输出的是乱码，因为intArray仅仅是一个地址引用。第四行输出真正的值，因为它经过了Arrays.toString()的转换。

----------  

## 从Array中创建ArrayList

<pre>
	String[] stringArray = {"a","b","c","d","e"};
	ArrayList<String> arrayList = new ArrayList<String>(Arrays.asList(stringArray));
	System.out.println(arrayList);//[a, b, c, d, e]
</pre>

为什么要将Array转换成ArrayList?因为ArrayList是动态链表，可以更方便地对ArrayList进行增删改，我们不需要循环Array将每一个元素加入到ArrayList中，用上述代码即可实现。

----------  

## 检查数组中是否包含某一个值

<pre>
	String[] stringArray = { "a", "b", "c", "d", "e" };
	boolean b = Arrays.asList(stringArray).contains("a");
	System.out.println(b);// true
</pre>

先使用Arrays.asList将Array转换成List<String>,这样就可以用动态链表的contains函数来判断元素是否包含在链表中。

----------  

## 连接两个数组  

<pre>
	int[] intArray = { 1, 2, 3, 4, 5 };
	int[] intArray2 = { 6, 7, 8, 9, 10 };
	// Apache Commons Lang library
	int[] combinedIntArray = ArrayUtils.addAll(intArray, intArray2);
</pre>

org.apache.commons.lang.ArrayUtils是Apache提供的数组处理类库，其addAll方法可以很方便地将两个数组连接成一个数组。

----------  

## 声明一个数组内链

<pre>
	method(new String[]{"a", "b", "c", "d", "e"});
</pre>

----------  

## 将数组中的元素以字符串的形式输出

<pre>
	String j = StringUtils.join(new String[] { "a", "b", "c" }, ", ");
	System.out.println(j);// a, b, c
</pre>

同样利用org.apache.commons.lang.StringUtils中的join方法，可以将数组中的元素以一个字符串的形式输出。

----------  

## 将Array转化成Set集合

<pre>
Set<String> set = new HashSet<String>(Arrays.asList(new String[]{"a", "b", "c", "d", "e"}));
System.out.println(set);//[d, e, b, c, a]
</pre>

在Java中使用Set，可以方便地将需要的类型以集合类型保存在一个变量中，主要应用在显示列表。同样可以先将Array转换成List，然后再将List转换成Set。

----------  

## 数组翻转

<pre>
	int[] intArray = { 1, 2, 3, 4, 5 };
	ArrayUtils.reverse(intArray);
	System.out.println(Arrays.toString(intArray));//[5, 4, 3, 2, 1]
</pre>

依然用到了万能的ArrayUtils。

----------  

## 从数组中移除一个元素

<pre>
	int[] intArray = { 1, 2, 3, 4, 5 };
	int[] removed = ArrayUtils.removeElement(intArray, 3);//create a new array
	System.out.println(Arrays.toString(removed));
</pre>


