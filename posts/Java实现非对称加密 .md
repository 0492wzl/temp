---
title: Java实现非对称加密 
date: 2016-08-18 16:45:13
categories: JAVA
tags: JAVA加解密

---

- 双保险
- 公钥，私钥
- DH(Diffie-Hellman)密钥交换算法
- RSA-基于因子分解
- ElGameal-基于离散对数
- ECC(Elliptical Curve Cryptography)-椭圆曲线加密


## DH
- 对称加密带来的困扰-传递密钥安全性
- 构建本地密钥
- 对称

| 密钥长度 | 默认 | 工作模式 | 填充方式 | 实现方 |
|:-------:|:---:|:--------:|:--------:|:-----:|
| 512-1024(64倍数) | 1024    | 无 | 无 | JDK |

