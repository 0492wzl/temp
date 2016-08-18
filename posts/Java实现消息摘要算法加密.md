---
title: Java实现消息摘要算法加密
date: 2016-04-07 11:07:00
categories: JAVA
tags: JAVA加解密

---

## 概述
- MD(Message Digest) 消息摘要
- SHA(Secure Hash Algorithm) 安全散列
- MAC(Message Authentication Code) 消息认证码

主要作用：验证数据完整性，是数字签名核心算法

<!--more-->

## 消息摘要算法-MD
- MD5
- MD家族(128位摘要信息)
	- MD2,MD4
- 特点均是128位

| 算法     | 摘要长度       | 实现方 |
|:--------:|:------------:|:-----:|
| MD2      | 128           | JDK 
| MD4      | 128           | Bouncy Castle 
| MD5      | 128           | JDK 

### 算法实现

- JDK MD2
<pre>
	MessageDigest md = MessageDigest.getInstance("MD2");  
	byte[] md2Bytes = md.digest(src.getBytes());  
	System.out.println(org.apache.commons.codec.binary.Hex.encodeHexString(md2Bytes));
</pre>

- JDK MD5
<pre>
	MessageDigest md = MessageDigest.getInstance("MD5");  
	byte[] md5Bytes = md.digest(src.getBytes());  
	System.out.println(org.apache.commons.codec.binary.Hex.encodeHexString(md5Bytes));
</pre>

- BouncyCastle MD2
<pre>
	org.bouncycastle.crypto.Digest digest = new org.bouncycastle.crypto.digests.MD2Digest();
	digest.update(src.getBytes(),0,src.getBytes().length);
	byte[] md2Bytes = new byte[digest.getDigestSize()];
	digest.doFinal(md2Bytes, 0);
	System.out.println(org.bouncycastle.util.encoders.Hex.toHexString(md2Bytes));
</pre>

- BouncyCastle MD4
方法一
<pre>
	org.bouncycastle.crypto.Digest digest = new  org.bouncycastle.crypto.digests.MD4Digest();
	digest.update(src.getBytes(),0,src.getBytes().length);
	byte[] md4Bytes = new byte[digest.getDigestSize()];
	digest.doFinal(md4Bytes, 0);
	System.out.println(org.bouncycastle.util.encoders.Hex.toHexString(md4Bytes));
</pre>
方法二(给JDK添加provider)
<pre>
	Security.addProvider(new  org.bouncycastle.jce.provider.BouncyCastleProvider());  
	MessageDigest md = MessageDigest.getInstance("MD4");  
	byte[] md4Bytes1 = md.digest(src.getBytes());  
	System.out.println(org.apache.commons.codec.binary.Hex.encodeHexString(md4Bytes1));
</pre>

- BouncyCastle MD5
<pre>
	org.bouncycastle.crypto.Digest digest = new org.bouncycastle.crypto.digests.MD5Digest();  
	digest.update(src.getBytes(),0,src.getBytes().length);
	byte[] md5Bytes = new byte[digest.getDigestSize()];
	digest.doFinal(md5Bytes, 0);
	System.out.println(org.bouncycastle.util.encoders.Hex.toHexString(md5Bytes));
</pre>		 
- commons.codec MD2(只对JDK MD2和MD5做了简化处理)
`org.apache.commons.codec.digest.DigestUtils.md2Hex(src.getBytes())`

- commons.codec MD5
`org.apache.commons.codec.digest.DigestUtils.md5Hex(src.getBytes())`

----------

## 消息摘要算法-SHA
- 安全散列算法
- 固定长度摘要信息
- SHA-1、SHA-2(SHA-224,SHA-256,SHA-384,SHA-512)

| 算法 | 摘要长度 | 实现方 |
|:-----:|:------:|:-----:|
| SHA-1 | 160    | JDK
| SHA-224 | 224    | Bouncy Castle 
| SHA-256 | 256    | JDK 
| SHA-384 | 384    | JDK 
| SHA-512 | 512    | JDK 
 
### 算法实现
- JKD SHA-1
<pre>
	MessageDigest sha = MessageDigest.getInstance("SHA");
	sha.update(src.getBytes());
	System.out.println("jdkSHA1:" + org.apache.commons.codec.binary.Hex.encodeHexString(sha.digest()));
</pre>
- BouncyCastle SHA-1
<pre>
	org.bouncycastle.crypto.Digest digest = new org.bouncycastle.crypto.digests.SHA1Digest();
	digest.update(src.getBytes(),0,src.getBytes().length);
	byte[] sha1Bytes = new byte[digest.getDigestSize()];
	digest.doFinal(sha1Bytes, 0);
	System.out.println("bcSHA1:" + org.bouncycastle.util.encoders.Hex.toHexString(sha1Bytes));
</pre>
- BouncyCastle SHA-224
方法一
<pre>
	org.bouncycastle.crypto.Digest digest = new org.bouncycastle.crypto.digests.SHA224Digest();
	digest.update(src.getBytes(),0,src.getBytes().length);
	byte[] sha224Bytes = new byte[digest.getDigestSize()];
	digest.doFinal(sha224Bytes, 0);
	System.out.println("bcSHA224:" + org.bouncycastle.util.encoders.Hex.toHexString(sha224Bytes));
</pre>
方法二(给JDK添加provider)
<pre>
	Security.addProvider(new BouncyCastleProvider());
	//...同BC MD4实现
</pre>
- commons.codec SHA-1
<pre>
	org.apache.commons.codec.digest.DigestUtils.sha1Hex(src.getBytes())
	或
	org.apache.commons.codec.digest.DigestUtils.sha1Hex(src)
</pre>

----------

## 消息摘要算法-MAC
- MAC(Message Authentication Code)
- HMAC(keyed-Hash Message Authentication Code),含有密钥的散列函数算法
- 融合MD,SHA
 - MD系列：HmacMD2,HmacMD4,HmacMD5
 - SHA系列：HmacSHA1,HmacSHA224,HmacSHA256,HmacSHA384,HmacSHA512
- 应用如SecureCRT(linux客户端)

| 算法     | 摘要长度 | 实现方     |
|:--------:|:-------:|:---------:|
| HmacMD2  | 128     | Bouncy Castle
| HmacMD4  | 128     | Bouncy Castle
| HmacMD5  | 128     | JDK          
| HmacSHA1  | 160     | JDK
| HmacSHA224  | 224     | Bouncy Castle
| HmacSHA256  | 256     | JDK 
| HmacSHA384  | 384     | JDK 
| HmacSHA512  | 512     | JDK 

### 算法实现
- JDK HmacMD5
<pre>
	javax.crypto.KeyGenerator keyGenerator = KeyGenerator.getInstance("HmacMD5");//初始化keyGenerator
	javax.crypto.SecretKey secretKey = keyGenerator.generateKey();//产生密钥
	//byte[] key = secretKey.getEncoded();//获得密钥
	byte[] key = Hex.decodeHex(new char[]{'a','a','a','a','a','A','A','A','A','A'});
	javax.crypto.SecretKey restoreKey = new SecretKeySpec(key, "HmacMD5");//还原密钥
	Mac mac = Mac.getInstance(restoreKey.getAlgorithm());//实例化mac
	mac.init(restoreKey);//初始化mac
	byte[] hmacMD5 = mac.doFinal(src.getBytes());//执行摘要
	System.out.println("jdkHmacMD5:" + Hex.encodeHexString(hmacMD5));
</pre>
- Bouncy Castle HmacMD5
<pre>
	org.bouncycastle.crypto.macs.HMac hmac = new HMac(new org.bouncycastle.crypto.digests.MD5Digest());
	hmac.init(new KeyParameter(org.bouncycastle.util.encoders.Hex.decode("aaaaaAAAAA")));
	hmac.update(src.getBytes(),0,src.getBytes().length);
	byte[] bcHmacBytes = new byte[hmac.getMacSize()];//执行摘要
	hmac.doFinal(bcHmacBytes, 0);
	System.out.println("bcHmacMD5:" + org.bouncycastle.util.encoders.Hex.toHexString(bcHmacBytes));
</pre>