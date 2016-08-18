---
title: Java实现非对称加密
date: 2016-04-19 16:45:13
categories: JAVA
tags: JAVA加解密

---

- 双保险(公钥，私钥)
- DH(Diffie-Hellman)密钥交换算法
- RSA-基于因子分解
- ElGameal-基于离散对数
- ECC(Elliptical Curve Cryptography)-椭圆曲线加密

<!--more-->

## DH
- 对称加密带来的困扰-传递密钥安全性
- 构建本地密钥
- 对称

| 密钥长度 | 默认 | 工作模式 | 填充方式 | 实现方 |
|:-------:|:---:|:--------:|:--------:|:-----:|
| 512-1024<br>(64倍数) | 1024    | 无 | 无 | JDK |

算法实现过程及相关类
- 初始化发送方密钥
 - KeyPairGenerator
 - KeyPair 通过KeyPairGenerator来得到类的对象
 - PublicKey
- 初始化接收方密钥
 - KeyFactory
 - X509EncodedKeySpec
 - DHPublicKey
 - DHParameterSpec
 - KeyPairGenerator
 - PrivateKey
- 构建密钥
 - KeyAgreement 提供密钥一致性（或密钥交换）协议的功能
 - SecretKey
 - KeyFactory
 - X509EncodedKeySpec
 - PublicKey
 - Cipher

实现代码:

<pre>
	private static void jdkHD(){
		try {
			//1.初始化发送方密钥
			KeyPairGenerator senderKeyPairGenerator = KeyPairGenerator.getInstance("DH");
			senderKeyPairGenerator.initialize(512);
			KeyPair senderKeyPair = senderKeyPairGenerator.generateKeyPair();
			//发送方公钥，发送给接收方（网络，文件。。。）
			byte[] sendPublicKeyEnc = senderKeyPair.getPublic().getEncoded();
　
			//2.初始化接收方密钥
			KeyFactory receiverKeyFactory = KeyFactory.getInstance("DH");
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(sendPublicKeyEnc);
			PublicKey receiverPublicKey = receiverKeyFactory.generatePublic(x509EncodedKeySpec);
			DHParameterSpec dhParameterSpec = ((DHPublicKey)receiverPublicKey).getParams();
			KeyPairGenerator receiverKeyPairGenerator = KeyPairGenerator.getInstance("DH");
			receiverKeyPairGenerator.initialize(dhParameterSpec);
			KeyPair receiverKeyPair = receiverKeyPairGenerator.generateKeyPair();
			PrivateKey receiverPrivateKey = receiverKeyPair.getPrivate();
			byte[] receiverPublicEnc = receiverKeyPair.getPublic().getEncoded();
　
			//3.密钥构建
			KeyAgreement receiverKeyAgreement = KeyAgreement.getInstance("DH");
			receiverKeyAgreement.init(receiverPrivateKey);
			receiverKeyAgreement.doPhase(receiverPublicKey, true);
			SecretKey receiverDesKey = receiverKeyAgreement.generateSecret("DES");
　
			KeyFactory senderKeyFactory = KeyFactory.getInstance("DH");
			x509EncodedKeySpec = new X509EncodedKeySpec(receiverPublicEnc);
			PublicKey senderPublicKey = senderKeyFactory.generatePublic(x509EncodedKeySpec);
			KeyAgreement senderKeyAgreement = KeyAgreement.getInstance("DH");
			senderKeyAgreement.init(senderKeyPair.getPrivate());
			senderKeyAgreement.doPhase(senderPublicKey, true);
			SecretKey senderDesKey = senderKeyAgreement.generateSecret("DES"); 
			if(Objects.equals(receiverDesKey, senderDesKey)){
				System.out.println("双方密钥相同");
			}
　
			//加密
			Cipher cipher = Cipher.getInstance("DES");
			cipher.init(Cipher.ENCRYPT_MODE, senderDesKey);
			byte[] result = cipher.doFinal(src.getBytes());
			System.out.println("jdk dh encrypt:" + Base64.encodeBase64String(result));
　
			//解密
			cipher = Cipher.getInstance("DES");
			cipher.init(Cipher.DECRYPT_MODE, receiverDesKey);
			result = cipher.doFinal(result);
			System.out.println("jdk dh decrypt:" + new String(result));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
</pre>

流程图:
![](http://oc1kexygx.bkt.clouddn.com/secret/DH.png)

另可参考：http://blog.csdn.net/sunny_sailor/article/details/7445649

----------  

## RSA
- 唯一广泛接受并实现
- 数据加密&数字签名
- 公钥加密，私钥解密
- 公钥解密，私钥加密

![](http://oc1kexygx.bkt.clouddn.com/secret/RSA.png)

实现代码:

<pre>
	public static void jdkRSA(){
		try {
			//1.初始化密钥
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
			keyPairGenerator.initialize(512);
			KeyPair keyPair = keyPairGenerator.generateKeyPair();
			RSAPublicKey rsaPublicKey = (RSAPublicKey) keyPair.getPublic();
			RSAPrivateKey rsaPrivateKey = (RSAPrivateKey) keyPair.getPrivate();
　
			System.out.println("Public Key :" + Base64.toBase64String(rsaPublicKey.getEncoded()));
			System.out.println("Private Key:" + Base64.toBase64String(rsaPrivateKey.getEncoded()));
　
			//2.私钥加密，公钥解密--加密
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(rsaPrivateKey.getEncoded());
			KeyFactory keyFactory = KeyFactory.getInstance("RSA");
			PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			Cipher cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.ENCRYPT_MODE, privateKey);
			byte[] result = cipher.doFinal(src.getBytes());
			System.out.println("私钥加密，公钥解密--加密:" + Base64.toBase64String(result));
　
			//3.私钥加密，公钥解密--解密
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(rsaPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("RSA");
			PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.DECRYPT_MODE, publicKey);
			result = cipher.doFinal(result);
			System.out.println("私钥加密，公钥解密--解密:" + new String(result));
　
			//4.公钥加密，私钥解密--加密
			x509EncodedKeySpec = new X509EncodedKeySpec(rsaPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("RSA");
			publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.ENCRYPT_MODE, publicKey);
			result = cipher.doFinal(src.getBytes());
			System.out.println("公钥加密，私钥解密--加密:" + Base64.toBase64String(result));
　
			//5.公钥加密，私钥解密--解密
			pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(rsaPrivateKey.getEncoded());
			keyFactory = KeyFactory.getInstance("RSA");
			privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			cipher = Cipher.getInstance("RSA");
			cipher.init(Cipher.DECRYPT_MODE, privateKey);
			result = cipher.doFinal(result);
			System.out.println("公钥加密，私钥解密--解密:" + new String(result));
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
</pre>

输出结果:

<pre>
	Public Key :MFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAJhp/glFCo8V/m0mkbAHUlJa12/vPhvx2xAhAMUyBxAcxiprMVcaOY08Q+trhtWx9cBrDsxq8tL29RphLDfsSncCAwEAAQ==
	Private Key:MIIBVAIBADANBgkqhkiG9w0BAQEFAASCAT4wggE6AgEAAkEAmGn+CUUKjxX+bSaRsAdSUlrXb+8+G/HbECEAxTIHEBzGKmsxVxo5jTxD62uG1bH1wGsOzGry0vb1GmEsN+xKdwIDAQABAkBdvuPn47Vo6QhDLrPxZ690xDc6vBahrZMmSoZkxC6f839ka2B/V0IUNtZwDhqC5ad9fDOdCENsxv2HGqiVzsRBAiEA0gW5+ohsdCqEEgncwr1CM9BmPTMFC32W8VOQYWuIKAcCIQC5x7mAnI9avySZOmBTUxqhFYdLgD/vjrhQZ5C9e/TOEQIgTk1YQ3HtjXn0d405w1kwf4TpOVP2Q4x1ZFAVelzPDK0CIQCtiLtlQFjD2mH0Y0wQ6UKpjzOGufB+86CFMohIBt6AEQIgOL01c731AODmonDEjfE+5WxsSexpcp5WOzjVfohFxFE=
	私钥加密，公钥解密--加密:I/vpb20iJaNXe5usBxMfqcLGZ72Im18C2sDMSqWZORv28LZNgGKgu8uaEtz0srzpJLKKZ0oc7bOIMrLgUzWoHQ==
	私钥加密，公钥解密--解密:hello world
	公钥加密，私钥解密--加密:KZK5TfZVGFp0G4u8EU93x2LvZRF0m+1/8bfh7IhwsNNkY97KDAr1/1sB6NPg8xqpKKesHyvPvL6+Ptv3UzpZZQ==
	公钥加密，私钥解密--解密:hello world
</pre>

流程图:
![](http://oc1kexygx.bkt.clouddn.com/secret/RSA-1.png)

----------  

## EIGamal
- 只有公钥加密算法

![](http://oc1kexygx.bkt.clouddn.com/secret/ELGamal.png)

实现代码:

<pre>
/**
	 *  
	 *  对于：“Illegal key size or default parameters”异常，是因为美国的出口限制，Sun通过权限文件（local_policy.jar、US_export_policy.jar）做了相应限制。因此存在一些问题：
	 *  Java 6 无政策限制文件：http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html
     *  Java 7 无政策限制文件：http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html
     *  我的时java7，自己安装的。
     *  /Library/Java/JavaVirtualMachines/jdk1.7.0_71.jdk/Contents/Home/jre/lib/security目录下，对应覆盖local_policy.jar和US_export_policy.jar两个文件。
     *  
     *  切换到%JDK_Home%\jre\lib\security目录下，对应覆盖local_policy.jar和US_export_policy.jar两个文件。同时，你可能有必要在%JRE_Home%\lib\security目录下，也需要对应覆盖这两个文件。
	 */
　
	// jdk实现：“私钥解密、公钥加密” ， 对于：“私钥加密、公钥解密”有问题，因为Elgamal不支持
	public static void jdkElgamal()
	{		
		try 
		{
			// 加入对BouncyCastle支持  
			Security.addProvider(new BouncyCastleProvider());
　
			// 1.初始化发送方密钥
			AlgorithmParameterGenerator algorithmParameterGenerator = AlgorithmParameterGenerator.getInstance("Elgamal");
			// 初始化参数生成器
			algorithmParameterGenerator.init(256);
			// 生成算法参数
			AlgorithmParameters algorithmParameters = algorithmParameterGenerator.generateParameters();
			// 构建参数材料
			DHParameterSpec dhParameterSpec = (DHParameterSpec)algorithmParameters.getParameterSpec(DHParameterSpec.class);
			// 实例化密钥生成器
			KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("Elgamal");	
			// 初始化密钥对生成器  
			keyPairGenerator.initialize(dhParameterSpec, new SecureRandom());
			KeyPair keyPair = keyPairGenerator.generateKeyPair();
			// 公钥
			PublicKey elGamalPublicKey = keyPair.getPublic();
			// 私钥 
			PrivateKey elGamalPrivateKey = keyPair.getPrivate();
			System.out.println("Public Key:" + Base64.encodeBase64String(elGamalPublicKey.getEncoded()));
			System.out.println("Private Key:" + Base64.encodeBase64String(elGamalPrivateKey.getEncoded()));
　
			// 2.私钥解密、公钥加密 ---- 加密
			// 初始化公钥  
	        // 密钥材料转换
			X509EncodedKeySpec x509EncodedKeySpec2 = new X509EncodedKeySpec(elGamalPublicKey.getEncoded());
			// 实例化密钥工厂
			KeyFactory keyFactory2 = KeyFactory.getInstance("Elgamal");
			// 产生公钥
			PublicKey publicKey2 = keyFactory2.generatePublic(x509EncodedKeySpec2);
			// 数据加密 
			// Cipher cipher2 = Cipher.getInstance("Elgamal");
			Cipher cipher2 = Cipher.getInstance(keyFactory2.getAlgorithm()); 
			cipher2.init(Cipher.ENCRYPT_MODE, publicKey2);
			byte[] result2 = cipher2.doFinal(src.getBytes());
			System.out.println("私钥加密、公钥解密 ---- 加密:" + Base64.encodeBase64String(result2));
　
			// 3.私钥解密、公钥加密 ---- 解密
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec5 = new PKCS8EncodedKeySpec(elGamalPrivateKey.getEncoded());
			KeyFactory keyFactory5 = KeyFactory.getInstance("Elgamal");
			PrivateKey privateKey5 = keyFactory5.generatePrivate(pkcs8EncodedKeySpec5);
//			Cipher cipher5 = Cipher.getInstance("Elgamal");
			Cipher cipher5 = Cipher.getInstance(keyFactory5.getAlgorithm()); 
			cipher5.init(Cipher.DECRYPT_MODE, privateKey5);
			byte[] result5 = cipher5.doFinal(result2);
			System.out.println("Elgamal 私钥加密、公钥解密 ---- 解密:" + new String(result5));
　
			/*	
			// 	私钥加密、公钥解密: 有问题
			// 4.私钥加密、公钥解密 ---- 加密
			PKCS8EncodedKeySpec pkcs8EncodedKeySpec = new PKCS8EncodedKeySpec(elGamalPrivateKey.getEncoded());
			KeyFactory keyFactory = KeyFactory.getInstance("Elgamal");
			PrivateKey privateKey = keyFactory.generatePrivate(pkcs8EncodedKeySpec);
			Cipher cipher = Cipher.getInstance("Elgamal");
			cipher.init(Cipher.ENCRYPT_MODE, privateKey);
			byte[] result = cipher.doFinal(src.getBytes());
			System.out.println("私钥加密、公钥解密 ---- 加密:" + Base64.encodeBase64String(result));
			
			// 5.私钥加密、公钥解密 ---- 解密
			X509EncodedKeySpec x509EncodedKeySpec = new X509EncodedKeySpec(elGamalPublicKey.getEncoded());
			keyFactory = KeyFactory.getInstance("Elgamal");
			PublicKey publicKey = keyFactory.generatePublic(x509EncodedKeySpec);
			cipher = Cipher.getInstance("Elgamal");
			cipher.init(Cipher.DECRYPT_MODE, publicKey);
			result = cipher.doFinal(result);
			System.out.println("Elgamal 私钥加密、公钥解密 ---- 解密:" + new String(result));
			*/
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
</pre>

流程图:
![](http://oc1kexygx.bkt.clouddn.com/secret/ELGamal-1.png)