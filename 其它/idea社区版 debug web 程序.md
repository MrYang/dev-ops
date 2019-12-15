## IntelliJ IDEA社区版 debug web 程序


### gradle

- 使用gradle构建一个符合maven标准的web程序

- 添加应用 gretty 插件

```groovy
buildscript {
    repositories {
        jcenter()
    }

    dependencies {
        classpath 'org.akhikhl.gretty:gretty:+'
    }
}

repositories {
    jcenter()
}

apply plugin: 'org.akhikhl.gretty'

dependencies {
    testCompile 'junit:junit:4.11'

    providedCompile "javax.servlet:javax.servlet-api:3.1.0"
    providedCompile "javax.servlet.jsp:jsp-api:2.2.1-b03"
}

gretty {
    servletContainer = 'jetty9'

    port = 8081
    contextPath = "gretty"

    managedClassReload = true
    fastReload = true

    jvmArgs = [
        "-Xms128M",
        "-Xmx512M"
    ]
}
```

- 添加一个remote Run/Debug Configurations 程序, 默认设置不用变，取名字如gretty-debug，然后保存。
- run appStartDebug 任务, 不要debug appStartDebug, 没用
- 使用快捷键Alt+Shift+F9 运行刚刚添加的remote Configurations gretty-debug
- 关闭的时候使用gretty 的appStop 任务， run appStop


### Maven

- 新建一个remote configurations, 设置address=8000, mvnDebug 默认的端口为8000，可以用`vi mvnDebug`查看具体端口
- 在模块目录命令行下运行命令`mvnDebug jetty:run`, 可以通过-D加各种java 参数
- 运行remote configuration