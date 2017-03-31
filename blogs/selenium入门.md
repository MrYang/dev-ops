## selenium 入门

- 下载[chromeDriver](https://sites.google.com/a/chromium.org/chromedriver/)

- 添加依赖

```xml
dependencies {
    compile "org.seleniumhq.selenium:selenium-java:3.3.1"
    compile "com.github.detro:phantomjsdriver:1.2.0"
}
```

- Java代码

    [chromeDriver文档](https://github.com/SeleniumHQ/selenium/wiki/ChromeDriver)， 确保chrome 的安装路径在默认安装路径

    `/usr/local/chromedriver` 为第一步下载的文件解压后放置的路径

```java
System.setProperty("webdriver.chrome.driver", "/usr/local/chromedriver");

ChromeDriverService service = ChromeDriverService.createDefaultService();
service.start();

WebDriver driver = new ChromeDriver(service);
driver.get("http://www.baidu.com");
WebElement element = driver.findElement(By.id("su"));
System.out.println(element.getText());

driver.quit();
service.stop();
```