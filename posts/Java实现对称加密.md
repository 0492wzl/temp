---
title: Java实现对称加密
date: 2016-04-18 14:29:13
categories: JAVA
tags: JAVA加解密

---

## 概念
- 加密密钥=解密密钥
- 初等的加密算法
- DES
 - 3DES
- AES
- PBE
- IDEA

<!--more-->

----------  

## DES
DES(Data Encryption Standard)数据加密标准

![](http://oc1kexygx.bkt.clouddn.com/secret/DES-1.png)


实现方式
- jdkDES

<pre>
	public static void jdkDES(){
		//生成key
		javax.crypto.KeyGenerator keyGenerator = KeyGenerator.getInstance("DES");
		keyGenerator.init(56);
		SecretKey secretKey = keyGenerator.generateKey();
		byte[] bytesKey = secretKey.getEncoded();
　
		//key转换
		javax.crypto.spec.DESKeySpec desKeySpec = new DESKeySpec(bytesKey);
		SecretKeyFactory factory = SecretKeyFactory.getInstance("DES");
		java.security.Key convertKey = factory.generateSecret(desKeySpec);
　
		//加密
		Cipher cipher = Cipher.getInstance("DES/ECB/PKCS5Padding");
		cipher.init(Cipher.ENCRYPT_MODE, convertKey);
		byte[] result = cipher.doFinal(src.getBytes());
		System.out.println("jdk des encrypt:" + org.apache.commons.codec.binary.Hex.encodeHexString(result));
　
		//解密
		cipher.init(Cipher.DECRYPT_MODE, convertKey);
		result = cipher.doFinal(result);
		System.out.println("jdk des dncrypt:" + new String(result));
	}
</pre>

- bcDES

<pre>
	public static void bcDES(){
		Security.addProvider(new BouncyCastleProvider());
　
		//生成key
		javax.crypto.KeyGenerator keyGenerator = KeyGenerator.getInstance("DES","BC");//此处需要添加provider
		keyGenerator.init(56);
		SecretKey secretKey = keyGenerator.generateKey();
		byte[] bytesKey = secretKey.getEncoded();
　
		//key转换
		javax.crypto.spec.DESKeySpec desKeySpec = new DESKeySpec(bytesKey);
		SecretKeyFactory factory = SecretKeyFactory.getInstance("DES");
		java.security.Key convertKey = factory.generateSecret(desKeySpec);
　
		//加密
		Cipher cipher = Cipher.getInstance("DES/ECB/PKCS5Padding");
		cipher.init(Cipher.ENCRYPT_MODE, convertKey);
		byte[] result = cipher.doFinal(src.getBytes());
		System.out.println("bc des encrypt:" + Hex.encodeHexString(result));
　
		//解密
		cipher.init(Cipher.DECRYPT_MODE, convertKey);
		result = cipher.doFinal(result);
		System.out.println("bc des dncrypt:" + new String(result));
	}
</pre>

工作流
![](http://oc1kexygx.bkt.clouddn.com/secret/DES.png)

----------  

## 3重DES
3重DES好处：
 - 密钥长度增加
 - 迭代次数提高
3DES(Triple DES或DESede)

![](http://oc1kexygx.bkt.clouddn.com/secret/3DES.png)

实现方式

- jdk3DES

<pre>
	private static void jdk3DES() throws Exception{
		//生成key
		javax.crypto.KeyGenerator keyGenerator = KeyGenerator.getInstance("DESede");
	//	keyGenerator.init(168);//112-168
		keyGenerator.init(new SecureRandom());
		
		SecretKey secretKey = keyGenerator.generateKey();
		byte[] bytesKey = secretKey.getEncoded();
　
		//key转换
		javax.crypto.spec.DESedeKeySpec desKeySpec = new DESedeKeySpec(bytesKey);
		SecretKeyFactory factory = SecretKeyFactory.getInstance("DESede");
		java.security.Key convertKey = factory.generateSecret(desKeySpec);
　
		//加密
		Cipher cipher = Cipher.getInstance("DESede/ECB/PKCS5Padding");
		cipher.init(Cipher.ENCRYPT_MODE, convertKey);
		byte[] result = cipher.doFinal(src.getBytes());
		System.out.println("jdk 3des encrypt:" + org.apache.commons.codec.binary.Hex.encodeHexString(result));
　
		//解密
		cipher.init(Cipher.DECRYPT_MODE, convertKey);
		result = cipher.doFinal(result);
		System.out.println("jdk 3des dncrypt:" + new String(result));
	}
</pre>

- bc3DES

<pre>
	与之前方法类似
</pre>

----------  

## AES
- AES是目前使用最多的对称加密算法
- 至今尚未有官方报道已经破解
- 通常用于移动通信系统加密以及基于SSH协议的软件
- 更加高级，是DES的替代者

![](http://oc1kexygx.bkt.clouddn.com/secret/AES-1.png)

实现方式

- jdkAES

<pre>
	private static void jdkAES() throws Exception{
　
		//生成key
		javax.crypto.KeyGenerator keyGenerator = KeyGenerator.getInstance("AES");
		keyGenerator.init(128);//new SecureRandom()
		SecretKey secretKey = keyGenerator.generateKey();
		byte[] bytesKey = secretKey.getEncoded();
　
		//key转换
		Key key = new SecretKeySpec(bytesKey, "AES");
　
		//加密
		Cipher cipher = Cipher.getInstance("AES/ECB/PKCS5Padding");
		cipher.init(Cipher.ENCRYPT_MODE, key);
		byte[] result = cipher.doFinal(src.getBytes());
		System.out.println("jdk aes encrypt:" + org.apache.commons.codec.binary.Hex.encodeHexString(result));
　
		//解密
		cipher.init(Cipher.DECRYPT_MODE, key);
		result = cipher.doFinal(result);
		System.out.println("jdk aes dncrypt:" + new String(result));
	}
</pre>

- bcAES

<pre>
	与之前方法类似
</pre>

工作流
![](http://oc1kexygx.bkt.clouddn.com/secret/AES.png)

----------  

## PEB
PEB算法结合了消息摘要算法和对称加密算法的优点

- PBE(Password Based Encryption) 基于口令加密
- 对已有算法的包装
- PBEWithMD5AndDES

![](http://oc1kexygx.bkt.clouddn.com/secret/PBE-1.png)
![](http://oc1kexygx.bkt.clouddn.com/secret/PBE-2.png)

实现方式

<pre>
	private static void jdkPBE() throws Exception{
		//初始化盐
		SecureRandom random = new SecureRandom();
		byte[] salt = random.generateSeed(8);
　
		//口令与密钥
		String password = "password";
		PBEKeySpec pbeKeySpec = new PBEKeySpec(password.toCharArray());
		SecretKeyFactory factory = SecretKeyFactory.getInstance("PBEWithMD5AndDES");
		Key key = factory.generateSecret(pbeKeySpec);
　
		//加密
		PBEParameterSpec pbeParameterSpec = new PBEParameterSpec(salt,100);
		Cipher cipher = Cipher.getInstance("PBEWithMD5AndDES");
		cipher.init(Cipher.ENCRYPT_MODE,key, pbeParameterSpec);
		byte[] result = cipher.doFinal(src.getBytes());
		System.out.println("jdk pbe encrypt:" + org.apache.commons.codec.binary.Hex.encodeHexString(result));
　
		//解密
		cipher.init(Cipher.DECRYPT_MODE, key, pbeParameterSpec);
		result = cipher.doFinal(result);
		System.out.println("jdk pbe dncrypt:" + new String(result));
	}
</pre>

- bcPBE

<pre>
	与之前方法类似
</pre>

工作流
![](http://oc1kexygx.bkt.clouddn.com/secret/PBE-3.png)

