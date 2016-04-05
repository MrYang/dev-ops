# Gradle

### 安装

- 需要预先安装jdk1.6版本以上
- 从gradle官网下载最新版本 解压之 `unzip gradle-all.zip -d /usr/local` 到`/usr/local`目录
- 编辑 `/etc/profile`文件，输入
```shell
export GRADLE_HOME=/usr/local/gradle
export PATH=$GRADLE_HOME/bin:$PATH
```
- 执行命令 `source /etc/profile` 使环境变量生效，测试是否安装成功`gradle -version`

### 使用

- 基本设置

```groovy
group "com.zz"
version "0.1"
apply plugin: "java"
apply plugin: "war"
```

- 设置编译级别

```groovy
sourceCompatibility=1.7
targetCompatibility=1.7
```

- 依赖

```groovy
dependencies {
    compile "org.springframework:spring-context:4.2.3.RELEASE"
	compile fileTree(dir: '../libs', include: '**/*.jar') // libs目录下所有的jar都依赖进来
	 compile('javax.servlet.jsp.jstl:jstl-api:1.2') {
        exclude(group:'javax.servlet', module: 'servlet-api') // 排除依赖
    }
    testCompile "junit:junit:4.+"
}
```

- 查看依赖 `gradle dependencies`
- 设置变量

```groovy
ext {
        springVersion = "4.2.5.RELEASE"

        if (project.hasProperty("profile")) {
            profile = project.properties["profile"]
        } else {
            profile = "dev";
        }
}
```

- 变量替换，类似于maven resource filter， 在引用的配置文件使用`@profile@`替换

```groovy
import org.apache.tools.ant.filters.ReplaceTokens
processResources {
        filter ReplaceTokens, tokens: [
                "profile": project.property('profile')
        ]

		include "application-config.xml"
		include "application-${profile}.properties"
}
```

- 配置中央仓库

```groovy
repositories {  
     maven {url "http://192.168.152.10:8082/nexus/content/groups/public"}
     maven {url "http://192.168.152.10:8082/nexus/content/repositories/thirdparty"}
     mavenCentral()
}
```

- 发布到私服， `gradle publish`，安装到本地仓库，`gradle publishToMavenLocal`

```groovy
apply plugin: 'maven-publish'

task sourceJar(type: Jar) {
  from sourceSets.main.java
  from sourceSets.main.resources
  classifier "sources"
}

publishing {
    repositories {
        maven {
			if(project.version.endsWith('-SNAPSHOT')) {
                url 'http://192.168.152.10:8082/nexus/content/repositories/snapshots'
            } else {
                url 'http://192.168.152.10:8082/nexus/content/repositories/releases'
            }
            credentials {
                username 'admin'
                password 'admin123'
            }
        }
    }
    publications {
		maven(MavenPublication) {
			artifacts = [jar, sourceJar]
        }
	}
}
```