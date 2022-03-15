# 安全开发笔记

## Java加密拓展(JCE)

### RSA

RSA 公钥是一个大整数，包含(n,e)，私钥是公钥的大整数的质因数，包含(n,d)

RSA 组成部分包括:

modulus         integer  n = p*q 相乘后二进制位密钥长度

publicExponent  integer  e 随机整数，通常为65537，或者3

privateExponent integer  d 私钥

prime1 integer p 质数p

prime2 integer q 质数q

exponent1 integer d mod (p-1)

exponent2 integer d mod (q-1)

RSA加密OAEP，PKCS1#5，推荐OAEP，加密结果每次不同

签名PSS，PKCS1#5，推荐PSS


## openssl 常用命令

- openssl ecparam -name SM2 -genkey -out sm.key 创建SM2私钥
- openssl req -new -x509 -sha256 -key sm2.key -out sm2.pem -days 365 -subj '/C=CN' 根据SM2私钥生成证书
- openssl x509 -in sm2.pem -ouout -text  查看证书
- opesssl ec -in sm2.key -pubout -noout -text 读取私钥内容
- openssl ec -in ec_prikey.pem -pubout -out ec_pubkey.pem 从私钥中读取公钥
- openssl rsa -in rsa_prikey.pem -pubout -out rsa_pubkey.pem
- openssl pkcs8 -topk8 -inform PEM -in rsa.key -outform PEM -nocrypt -out rsapkcs8.key  将pkcs1类型私钥转成pkcs8类型私钥

- openssl req -x509 -new -days 365 -key rsa.key -out cert.crt 创建证书

## cheat.sh

- curl cht.sh/openssl 查看openssl 使用方法

