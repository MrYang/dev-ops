- 配置 pom.xml

```xml
<scm>
        <url>http://repoIp/svn/repo/trunk/sub-project</url>
        <connection>scm: svn: http://repoIp/svn/repo/trunk/sub-project</connection>
       <developerConnection>scm: svn: http://repoIp/svn/repo/trunk/sub-project</developerConnection>
</scm>
```

```xml
<build>
    <plugins>
    	<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <configuration>
                    <!-- 设置分支跟tag的url -->
                    <branchBase>http://repoIp/svn/repo/branches</branchBase>
                    <tagBase>http://repoIp/svn/repo/tags</tagBase>
                </configuration>
            </plugin>
	</plugins>
</build>
```

`mvn release:prepare` 根据提示操作，发布前的一些操作，如修改版本号，设置新版本号等。

`mvn release:rollback` 回滚

`mvn release:perform`  自动签出刚才打的tag，然后打包，分发到远程Maven仓库中