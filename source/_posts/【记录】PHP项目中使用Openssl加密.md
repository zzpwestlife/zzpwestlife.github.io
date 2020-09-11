---
title: 【记录】PHP 项目中使用 Openssl 加密
date: 2017-04-17
tags: openssl
description: 
---

首先确保openssl已正确安装， `openssl verison`

进入想要生成公钥和私钥的目录


### 1. 生成RSA私钥
<!--more-->

```
# 进入 openssl 客户端
$ openssl

# 生成1024位的私钥,不指定的话默认2048位
OpenSSL> genrsa -out rsa_private_key.pem 1024

# 如果提示下面的错误：unable to write 'random state'
e is 65537 (0x10001)，执行下面的语句
$ sudo rm ~/.rnd

# 重新执行，在该目录就能看到生成的私钥
OpenSSL> genrsa -out rsa_private_key.pem 1024

```

### 2. 把RSA私钥转换成PKCS8格式

```
OpenSSL> pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM –nocrypt

$ touch rsa_private_key_pkcs8.pem
# 将生成的私钥粘贴，并保存
```

### 3. 生成RSA公钥

```
OpenSSL> rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
```

此时，我们可以看到一个文件名为`rsa_public_key.pem`的文件，打开它，可以看到`-----BEGIN PUBLIC KEY-----`开头，
`-----END PUBLIC KEY-----`结尾的没有换行的字符串，这个就是公钥。

### 4. 在项目中使用

```php
public function opensslEncrypt($data)
{
    // 获取公匙
    $publicKeyPath = app_path('Knowledge/OpensslKeys/') . 'rsa_public_key.pem';
    //这个函数可用来判断公钥是否是可用的
    $publicKey = openssl_pkey_get_public(file_get_contents($publicKeyPath));

//        $privateKeyPath = app_path('Knowledge/OpensslKeys/') . 'rsa_private_key.pem';
    //这个函数可用来判断私钥是否是可用的，可用返回资源id Resource id
//        $privateKey = openssl_pkey_get_private(file_get_contents($privateKeyPath));

    // 使用公钥加密，客户端使用私钥解密
    if (!is_empty($publicKey)) {
        $encrypted = "";
        $decrypted = "";
//            openssl_private_encrypt($data, $encrypted, $privateKey);//私钥加密
//            $encrypted = base64_encode($encrypted);//加密后的内容通常含有特殊字符，需要编码转换下，在网络间通过url传输时要注意base64编码是否是url安全的
//
//            $decrypted = "";
//            openssl_public_decrypt(base64_decode($encrypted), $decrypted, $publicKey);//私钥加密的内容通过公钥可解密出来
//            dd($decrypted);


        openssl_public_encrypt($data, $encrypted, $publicKey);//公钥加密
        return base64_encode($encrypted); // 加密后的内容通常含有特殊字符，需要编码转换下，在网络间通过url传输时要注意base64编码是否是url安全的
    }
}
        
```


