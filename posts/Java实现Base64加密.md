---
title: Java实现Base64加密
date: 2016-07-25 22:56:13
categories: JAVA
tags: JAVA加解密

---

## 安全组成
- **JCA(Java Cryptography Architecture)**  
提供基本的加密框架，如消息摘要、数字签名等
- **JCE(Java Cryptography Extension)**  
在JCA基础上做了扩展，提供了很多加密的算法、消息摘要、密钥管理的功能。如DES、AES、RSA算法均通过JCE提供。在JDK包中
- **JSSE(Java Secure Socket Extension)**  
提供基于SSL的加密功能，主要用于网络传输，
- **JAAS(Java Authentication and Authentication Service)**  
提供了在Java平台上进行用户的身份验证的功能，即基于Java开发的系统的权限和安全是基于JAAS。
<!--more-->

- 使用JDK以外的扩展包需要修改资源文件并增加相关的内容，这是使用JDK意外的扩展包的方式之一(..\jre7\lib\security)
 1. security.provider.1=sun.security.provider.Sun
 2. security.provider.2=com.sun.net.ssl.internal.ssl.Provider
 3. security.provider.3=com.sun.rsajca.Provider
 4. security.provider.4=com.sun.crypto.provider.SunJCE
 5. security.provider.5=sun.security.jgss.SunProvider
 6. security.provider.6=org.bouncycastle.jce.provider.BouncyCastleProvider

----------

## 相关java包、类
- java.security 消息摘要
- javax.crypto 安全消息摘要，消息认证（鉴别）码
- java.net.ssl 安全套接字（网络传输相关，HttpsURLConnection、SSLContext）

----------

## 第三方java扩展
- Bouncy Castle 两种支持方案：1）配置；2）调用
- Commons Codec(Apache) Base64、二进制、十六进制、字符集编码、Url编码/解码 

----------

## Base64算法

### 实现
<pre>
	encoder:aW1vb2Mgc2VjdXJpdHkgYmFzZTY0
	decoder:imooc security base64`
</pre>

 - JDK实现  
<pre>
	import sun.misc.BASE64Encoder;
　
	BASE64Encoder encoder = new BASE64Encoder();
	String encode = encoder.encode(src.getBytes());
	System.out.println("encoder:" + encode);
	BASE64Decoder decoder = new BASE64Decoder();
	System.out.println("decoder:" + new String(decoder.decodeBuffer(encode)));
</pre>

 - Commons Codec(commons-codec-1.10.jar)
<pre>
	import org.apache.commons.codec.binary.Base64;
　
	byte[] encodeBytes = Base64.encodeBase64(src.getBytes());
	System.out.println("encoder:" + new String(encodeBytes));
	byte[] decodeBytes = Base64.decodeBase64(encodeBytes);
	System.out.println("decoder:" + new String(decodeBytes));
</pre>


 - Bouncy Castle(bcprov-jdk15on-149.jar)
<pre>
	import org.bouncycastle.util.encoders.Base64;
　
	byte[] encodeBytes = Base64.encode(src.getBytes());
	System.out.println("encoder:" + new String(encodeBytes));
	byte[] decodeBytes = Base64.decode(encodeBytes);
	System.out.println("decoder:" + new String(decodeBytes));
</pre>

### 应用场景
(1)e-mail
(2)密钥
(3)证书文件
### 产生：邮件的"历史问题"
### 定义：基于64个字符的编码算法
值0 1 2 3...63 (pad) 分别对应码 A~Z  a~z  0~9  +  /  =
### 衍生：Base16、Base32、Url Base64