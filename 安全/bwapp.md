# bwapp

buggy web Application 是一个集成了各种常见漏洞和最新漏洞的开源Web应用程序，帮助网络安全爱好者、开发人员和学生发现并防止网络漏洞

DVWA（Damn Vulnerable Web Application）是一个用来进行安全脆弱性鉴定的PHP/MySQL Web应用，为安全专业人员测试自己的专业技能和工具提供合法的环境，帮助web开发者更好的理解web应用安全防范的过程

## 安装

### bwapp

`docker pull raesene/bwapp`  

`http://www.itsecgames.com/` 官网


运行 `docker run -d -p 8000:80 --name bwapp raesene/bwapp`   默认使用`bee/bug`账户密码访问

第一次访问 `http://localhost:8000/install.php` 安装


### dvwa

`docker pull citizenstig/dvwa`

`http://www.dvwa.co.uk/` 官网

运行 `docker run -d -p 8001:80 --name dvwa citizenstig/dvwa`   默认使用`admin/password`账户密码访问