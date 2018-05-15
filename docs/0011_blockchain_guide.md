# blockchain guide

## 参考文档

* [blockchain Guide](./refers/blockchain_guide.pdf)
* [区块链和 HyperLedger 微讲堂系列](http://list.youku.com/albumlist/show/id_49106065.html?)
* [IBM Blockchain Platform: 开发](https://developer.ibm.com/cn/blockchain/sandbox/)

## 区块链分类

根据参与者的不同，可以分为公有（Public）链、联盟（Consortium）链和私有（Private）链。

* 公有链，顾名思义，任何人都可以参与使用和维护，典型的如比特币区块链，信息是完全公开的。
* 私有链，由集中管理者进行管理限制，只有内部少数人可以使用，信息不公开。
* 联盟链则介于两者之间，由若干组织一起合作维护一条区块链，该区块链的使用必须是带有权限的限制访问，相关信息会得到保护，典型如供应链机构或银行联盟。

## 密码学技术

* Hash 算法
  * hash值在应用中又被称为指纹（fingerprint）、摘要（digest）。
  * MD5（Message Digest 5）是一个经典的hash算法，其和SHA-1算法都已被证明安全性不足应用于商业场景。
  * 目前流行的 Hash 算法包括 MD5、SHA-1 和 SHA-2。
  * 数字摘要是解决确保内容没被篡改过的问题（利用 Hash 函数的抗碰撞性特点）。
* 加解密算法
  * 对称加密
  * 非对称加密
* 数字签名

## 分叉（Fork）

