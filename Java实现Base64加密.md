---
title: Java实现Base64加密
date: 2016-07-25 22:56:13
categories: JAVA
tags: JAVA加解密

---

## 密码常用术语
### （一）
- 明文：待加密信息
- 密文：经过加密后的明文
- 加密：明文转为密文的过程
- 加密算法：明文转为密文的转换算法
- 加密密钥：通过加密算法进行加密操作用的密钥
- 解密：将密文转为明文的过程
- 解密算法：密文转为明文的算法
- 解密密钥：通过解密算法进行解密操作用的密钥  

<!--more-->

### （二）
- 密码分析：截获密文者试图通过分析截获的密文从而推断出原来的明文或密钥的过程
- 主动攻击：攻击者非法入侵密码系统，才用伪造、修改、删除等手段向系统注入假消息进行欺骗。（对密文具有破坏作用）
- 被动攻击：对一个保密系统采取截获密文并对其进行分析和攻击。（对密文没有破坏作用）
- 密码体制：由明文空间、密文空间、密钥空间、加密算法和解密算法五部分组成。

### （三）
- 密码协议：也称安全协议，指以密码学为基础的消息交换的通信协议，目的是在网络环境中提供安全的服务。
- 密码系统：指用于加密、解密的系统。
- 柯克霍夫原则：数据的安全基于密钥而不是算法的保密。即系统的安全取决于密钥，对密钥保护，对算法公开。--现代密码学设计的基本原则。（督促算法的进步，同时审查当前算法是否存在不足）


----------

## 密码分类
### 时间
- 古典密码：以字符为几本加密单元
- 现代密码：以信息块为几本加密单元

### 保密内容算法

| 名称          | 详细说明       | 应用领域  | 类别    |
|:-------------:|:-------------:|:-----:|:-----------:|
| 受限制算法     | 算法的保密性基于保持算法的密码 | 军事领域 | 古典密码 
| 基于密钥的算法     | 算法的保密性基于对密钥的保密 |    | 现代密码
### 密码体制
| 名称 | 别名 | 详细说明 
|:-------:|:-------:|:------:|
| 对称密码 | 单钥密码或私钥密码 | 指加密密钥与解密密钥相同 
|非对称密码|双钥密码或公钥密码 | 指加密密钥与解密密钥不同，密钥分公钥匙、私钥|
|对称密码算法|单钥密码算法或私钥密码算法|指应用于对称密码的加密、解密算法。|
|非对称密码算法|双钥密码算法或公钥密码算法|指对应于非堆成密码的加密、解密算法。|

### 明文处理方法
- 分组加密：指加密时将明文分成固定长度的组，用同一密钥和算法对每一块加密，输入也是固定长度的密文。对用于网络加密。
- 流密码：也称序列密码。指加密时每次加密一位或者一个字节明文。


----------

## 散列函数
### 用来验证数据的完整性。
### 特点
- 长度不受限制
- 哈希值容易计算
- 散列运算过程不可逆
 
### 相关算法
- 消息摘要算法MD5等
- SHA--安全散列算法
- MAC--消息认证码算法（MAC--苹果操作系统）
 

----------

## 数字签名
- 主要是针对以数字的形式存储的消息进行的处理  
- 会产生一个带有操作者身份信息的编码  
- 执行数字前面的实体签名者，签名过程做的算法叫签名算法

----------

## OSI安全体系（Open System Interconnection）
### 网络通讯
1. 物理层
2. 数据链路层
3. 网络层
4. 传输层
5. 会话层
6. 表示层
7. 应用层

### 安全机智
1. 加密机制
2. 数字签名机制
3. 访问控制机制
4. 数据完整性机制
5. 认证机制
6. 业务流填充机制
7. 路由控制机制
8. 公证机制

### 安全服务
1. 认证（鉴别）
2. 访问控制服务
3. 数据保密性服务
4. 数据完整性服务
5. 抗否认性服务

----------

## TCP/IP安全体系
### 网络通讯
1. 网络接口层		对应OSI(1,2)
2. 网络层		对应OSI(3)
3. 传输层		对应OSI(4)
4. 应用层		对应OSI(5,6,7)

### 安全体系结构（与网络通讯层一一对应）
1. 网络接口层--网络接口层安全
2. 网络层--网络层安全
3. 传输层--传输层安全
4. 应用层--应用层安全

### 安全服务与安全机制对应关系
抗否认性服务---8公证机制
数据完整性服务---4数据完整性机制
数据保密性服务---1加密机制、6业务流填充机制
访问控制服务---3访问控制机制、7路由控制机制
认证（鉴别）服务---5认证机制、2数字签名机制

----------

## Java
### 安全组成
- **JCA(Java Cryptography Architecture)**  
提供基本的加密框架，如消息摘要、数字签名等
- **JCE(Java Cryptography Extension)**  
在JCA基础上做了扩展，提供了很多加密的算法、消息摘要、密钥管理的功能。如DES、AES、RSA算法均通过JCE提供。在JDK包中
- **JSSE(Java Secure Socket Extension)**  
提供基于SSL的加密功能，主要用于网络传输，
- **JAAS(Java Authentication and Authentication Service)**  
提供了在Java平台上进行用户的身份验证的功能，即基于Java开发的系统的权限和安全是基于JAAS。

- 使用JDK以外的扩展包需要修改资源文件并增加相关的内容，这是使用JDK意外的扩展包的方式之一(..\jre7\lib\security)
 1. security.provider.1=sun.security.provider.Sun
 2. security.provider.2=com.sun.net.ssl.internal.ssl.Provider
 3. security.provider.3=com.sun.rsajca.Provider
 4. security.provider.4=com.sun.crypto.provider.SunJCE
 5. security.provider.5=sun.security.jgss.SunProvider
 6. security.provider.6=org.bouncycastle.jce.provider.BouncyCastleProvider

### 相关java包、类
- java.security 消息摘要
- javax.crypto 安全消息摘要，消息认证（鉴别）码
- java.net.ssl 安全套接字（网络传输相关，HttpsURLConnection、SSLContext）

### 第三方java扩展
- Bouncy Castle 两种支持方案：1）配置；2）调用
- Commons Codec(Apache) Base64、二进制、十六进制、字符集编码、Url编码/解码 

### Base64算法

1. 实现
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

2. 应用场景
(1)e-mail
(2)密钥
(3)证书文件
3. 产生：邮件的"历史问题"
4. 定义：基于64个字符的编码算法
值0 1 2 3...63 (pad) 分别对应码 A~Z  a~z  0~9  +  /  =
5. 衍生：Base16、Base32、Url Base64