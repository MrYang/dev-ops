## Maven

### Maven��װ����
- �ӹ�������maven��ѹ��`/usr/local`Ŀ¼

	`tar -xvf maven.tar.gz -C /usr/local`

- Maven��װ����

	`vim /etc/profile`������������
```shell
export M2_HOME=/usr/local/apache-maven
export PATH=$M2_HOME/bin:$PATH
```
���ִ������ `source /etc/profile` ʹ����������Ч
�����Ƿ�װ�ɹ�`mvn -version`

### Maven��������

- `mvn clean compile package -Dmaven.test.skip=true -Ptest -f /root/test/pom.xml` ��������������Ԫ���ԣ�ʹ��test����
- `mvn install` ��װ�����زֿ�
- `mvn install:install-file -DgroupId=com.sun.jdmk -DartifactId=jmxtools -Dversion=1.2.1 -Dpackaging=jar -Dfile=/path/to/file` ��װjar�����زֿ�
- `mvn deploy` ������˽��
- `mvn deploy:deploy-file -DgroupId=com.sun.jdmk -DartifactId=jmxtools -Dversion=1.2.1 -Dpackaging=jar -Dfile=/path/to/file` ����jar��˽��

### Maven���
- compile
```xml
<plugin>
           <groupId>org.apache.maven.plugins</groupId>
           <artifactId>maven-compiler-plugin</artifactId>
           <version>3.0</version>
           <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                    <showWarnings>true</showWarnings>
            </configuration>
</plugin>
```

- resources
```xml
<resources>
            <resource>
                <directory>src/main/resources</directory>
				<!-- ʹ��resources�滻����-->
                <filtering>true</filtering>
                <includes>
                    <include>${profile.active}/application.properties</include>
                    <include>${profile.active}/logback.xml</include>
                    <include>*.xml</include>
                    <include>*.properties</include>
                </includes>
            </resource>
</resources>
```

- war
```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-war-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <warName>ROOT</warName>
                    <webResources>
                        <resource>
                            <filtering>true</filtering>
                            <directory>src/main/webapp</directory>
                            <includes>
                                <include>**/*.xml</include>
                            </includes>
                        </resource>
                    </webResources>
                    <archive>
                        <addMavenDescriptor>false</addMavenDescriptor>
                    </archive>
                </configuration>
</plugin>
```

- jar
```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <classesDirectory>target/classes/</classesDirectory>
                    <archive>
                        <manifest>
                            <mainClass>com.alibaba.dubbo.container.Main</mainClass>
                            <!-- ��������dubbo�ٷ��ṩ�� -->
                            <!-- ���ʱ MANIFEST.MF�ļ�����¼��ʱ����汾 -->
                            <useUniqueVersions>false</useUniqueVersions>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                        </manifest>
                        <manifestEntries>
                            <Class-Path>.</Class-Path>
                        </manifestEntries>
                    </archive>
                </configuration>
</plugin>
```

- dependency
```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy-dependencies</id>
                        <phase>package</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <type>jar</type>
                            <includeTypes>jar</includeTypes>
                            <excludeScope>system</excludeScope>
                            <outputDirectory>
                                ${project.build.directory}/lib
                            </outputDirectory>
                        </configuration>
                    </execution>
                </executions>
</plugin>
```

- scm
```xml
<scm>
        <url>http://svnIp/repo/trunk/project/subproject</url>
        <connection>scm:svn:http://svnIp/repo/trunk/project/subproject</connection>
        <developerConnection>scm:svn:http://svnIp/repo/trunk/project/subproject</developerConnection>
</scm>
```

- release
```xml
<plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-release-plugin</artifactId>
                <configuration>
                    <!-- ���÷�֧��tag��url -->
                    <branchBase>http://svnIp/repo/branches</branchBase>
                    <tagBase>http://svnIp/repo/tags</tagBase>
                </configuration>
</plugin>
```

### Mavenʵս
- ��ִ��jar��
	- ����Ŀ��������������jar�������һ��jar
	���assembly���
	```xml
<plugin>  
            <groupId>org.apache.maven.plugins</groupId>  
            <artifactId>maven-assembly-plugin</artifactId>  
            <version>2.6</version>  
            <configuration>  
                <archive>  
                    <manifest>  
                        <mainClass>com.alibaba.dubbo.container.Main</mainClass>  
                    </manifest>  
                </archive>  
                <descriptorRefs>  
                    <descriptorRef>jar-with-dependencies</descriptorRef>  
                </descriptorRefs>  
            </configuration>  
</plugin>
```
ִ��`mvn assembly:assembly`����ɡ�
	- ����Ŀ������jar���Ƶ�һ��Ŀ¼�в���MANIFEST�ļ������Class-Path��Main-Class
	�μ������е�jar�����dependency���

### Maven����
[mirror��repository ����](http://m.oschina.net/blog/100634)
[pom.xml����˵��](http://siye1982.iteye.com/blog/321557)