# Understand HTTPS Transfer

## Refers

* [图解HTTPS](http://www.cnblogs.com/zhuqil/archive/2012/07/23/2604572.html)

## Data Map

![img/HTTPS_Transfer.png](img/HTTPS_Transfer.png)

## Analysis

* 浏览器发起HTTPS请求连接到支持HTTPS请求的Web Server上；
* 支持HTTPS请求的Web Server返回公钥加密的Public Key（本来就是公开的，所以无所谓别人知道不知道）；
* 验证公钥的有效性（CA认证过的Public Key，当然有效，其他的就不一定了）；
* 浏览器生成对称加密的随机数（浏览器自己做，不需要浏览器使用者去做这件事情）；
* 浏览器用从服务端获取的Public Key对对称机密随机数进行加密；
* 发送Public Key加密后的数据给服务端；
* 服务端使用私钥对数据进行解密，提取对称加密的秘钥；
* 使用对称加密秘钥对网页内容进行加密；
* 发送加密后的网页内容给浏览器；
* 浏览器获取到加密后的网页内容后，使用对称加密的秘钥（本来就是自己生成的，自然就是知道的）进行解密；
