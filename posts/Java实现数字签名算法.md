---
title: Java实现数字签名算法
date: 2016-04-30 22:56:13
categories: JAVA
tags: JAVA加解密

---

- 带有密钥（公钥、私钥）的消息摘要算法。
- 验证数据的完整性、认证数据的来源、抗否认。
- OSI参考模型
- 私钥签名，公钥验证
- RAS、DSA、ECDSA

<!--more-->

## RSA
- 经典算法
- MD、SHA两类

![](http://oc1kexygx.bkt.clouddn.com/secret/RSA-2.png)

实现代码：

<pre>
	public static void jdkRSA()
	{
		try {
			// 1.初始化密钥
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
			keyPairGenerator.initialize(512);
			KeyPair keyPair = keyPairGenerator.generateKeyPair();
			RSAPublicKey rsaPublicKey = (RSAPublicKey)keyPair.getPublic();
			RSAPrivateKey rsaPrivateKey = (RSAPrivateKey)keyPair.getPrivate();
　
			// 2.进行签名
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(rsaPrivateKey.getEncoded());
			KeyFactory keyFactory = KeyFactory.getInstance("RSA");
			PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			Signature signature = Signature.getInstance("MD5withRSA");
			signature.initSign(privateKey);
			signature.update(src.getBytes());
			byte[] result = signature.sign();
			System.out.println("jdk rsa sign:" + Hex.encodeHexString(result) );
　　
			// 3.验证签名
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(rsaPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("RSA");
			PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			signature = Signature.getInstance("MD5withRSA");
			signature.initVerify(publicKey);
			signature.update(src.getBytes());
			boolean bool = signature.verify(result);
			System.out.println("jdk rsa verify:" + bool);
		} catch (Exception e) {
			System.out.println(e.toString());
		}
	}
</pre>

流程图：
![](http://oc1kexygx.bkt.clouddn.com/secret/RSA-3.png)

----------  

## DSA
- DSS(Digital Signature Standard)数字签名标准
- DSA(Digital Signature Algorithm)数字签名算法
- DSA仅包含数字签名

![](http://oc1kexygx.bkt.clouddn.com/secret/DSA.png)

实现代码：

<pre>
	public static void jdkDSA()
	{
		try {
			// 1.初始化密钥
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("DSA");
			keyPairGenerator.initialize(512);
			KeyPair keyPair = keyPairGenerator.generateKeyPair();
			DSAPublicKey dsaPublicKey = (DSAPublicKey)keyPair.getPublic();
			DSAPrivateKey dsaPrivateKey = (DSAPrivateKey)keyPair.getPrivate();
　
			// 2.进行签名
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(dsaPrivateKey.getEncoded());
			KeyFactory keyFactory = KeyFactory.getInstance("DSA");
			PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			Signature signature = Signature.getInstance("SHA1withDSA");
			signature.initSign(privateKey);
			signature.update(src.getBytes());
			byte[] result = signature.sign();
			System.out.println("jdk dsa sign:" + Hex.encodeHexString(result) );
　
			// 3.验证签名
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(dsaPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("DSA");
			PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			signature = Signature.getInstance("SHA1withDSA");
			signature.initVerify(publicKey);
			signature.update(src.getBytes());
			boolean bool = signature.verify(result);
			System.out.println("jdk dsa verify:" + bool);
		} catch (Exception e) {
			System.out.println(e.toString());
		}
	}	
</pre>

流程图：
![](http://oc1kexygx.bkt.clouddn.com/secret/DSA-1.png)

----------  

## ECDSA
- ECDSA(Elliptic Curve Digital Signature Algorithm)椭圆曲线数字签名算法
- 微软软件序列号
- 速度快，强度高，签名短

![](http://oc1kexygx.bkt.clouddn.com/secret/ECDSA.png)

实现代码：

<pre>
	public static void jdkECDSA()
	{
		try {
			// 1.初始化密钥
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("EC");
			keyPairGenerator.initialize(256);
			KeyPair keyPair = keyPairGenerator.generateKeyPair();
			ECPublicKey ecPublicKey = (ECPublicKey)keyPair.getPublic();
			ECPrivateKey ecPrivateKey = (ECPrivateKey)keyPair.getPrivate();
　
			// 2.进行签名
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(ecPrivateKey.getEncoded());
			KeyFactory keyFactory = KeyFactory.getInstance("EC");
			PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			Signature signature = Signature.getInstance("SHA1withECDSA");
			signature.initSign(privateKey);
			signature.update(src.getBytes());
			byte[] result = signature.sign();
			System.out.println("jdk ecdsa sign:" + Hex.encodeHexString(result) );
　
			// 3.验证签名
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(ecPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("EC");
			PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			signature = Signature.getInstance("SHA1withECDSA");
			signature.initVerify(publicKey);
			signature.update(src.getBytes());
			boolean bool = signature.verify(result);
			System.out.println("jdk ecdsa verify:" + bool);
		} catch (Exception e) {
			System.out.println(e.toString());
		}
	}
</pre>

流程图：
![](http://oc1kexygx.bkt.clouddn.com/secret/ECDSA-1.png)
