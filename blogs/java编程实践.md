## Java编程最佳实践

阅读前请参考 [Google Java编程风格](http://www.hawstein.com/posts/google-java-style.html)

- 通用准则
	- 命名一定要准确(随时查字典)，**有意义**，不允许使用flag,val,data,temp之类的名字，for,while循环中可以使用i,j,k之类的命名，boolean类型可以使用is,has开头，方法命名可以使用动词加名词的方式
	- 局部变量定义在离使用它最近的地方
	- 嵌套不要超过三层(包括try,catch)，如果必须要三层，请检查一下看哪里需要重构的
	- 代码块不要过大，特别是多个if elseif之间，多个if elseif中间的代码块可以使用一个方法重构
	- 局部变量不推荐重复使用，不推荐用同一个变量做不同的用途
	- 注释，不写无用及误解的注释，特别是一些方法注释的参数
	- 空行，在逻辑有一些相差的代码之间可以使用空行间隔开

- 异常
	- 没有必要的try,catch，如try，catch正常执行的代码
	- 不要catch Exception， 也不要throw Exception
	- 多抛出运行期异常，注意受检异常与运行期异常的区别
	- 方法定义尽量不要抛异常，在方法体内把受检异常转换成运行期异常
	- try,catch块不要太大
	- catch 了异常一定要抛出来，除非你知道自己在干什么
	- 不要在finally中返回

- 类，对象及方法
	- 方法参数提前验证
	- 函数提前返回，避免多次判断。
	- 类，方法写小一些，职责明确
	- 包装类型与基本类型，包装类型的toString方法，注意null异常
	- 方法返回值尽量不要为null，如果确实返回为null的，需要写注释说明一下，特别说明返回empty的list，而不要返回null list

- 集合
	- mybatis返回的list都不是不为null的空集合，所以不需要判断为null或者size>0

- 开源
	- 大胆使用一些开源类库，包括apache-commons,guava, joda,各种集合库

------------------------------------------------------------------------------------------------

##代码参考

- **命名规则**

**bad**
```java
boolean flag = false;
Order temp = new Orderl();
```
**Good**
```java
boolean isExists = false;
Order order= new Order();
```
- **变量定位位置**

**Bad**
```java
int count = 0;
// 10行代码
count = countByParams(params);
```
**Good**
```java
int count = countByParams(params);
```
- **小代码块**

**Bad**
```java
if (a == 10) {
		// 100行
} else if (a == 20) {
		// 200行
} else {
		// 100行
}
```
**Good**
```java
if (a == 10) {
		handleTen(params);
} else if (a == 20) {
		handleTwenty(params)
} else {
		handleOther(params);
}
```
- **局部变量重复使用**

**Bad**
```java
Response resp = getAResponse();
// 其他代码
resp = getBResponse();
return resp;
```
**Good**
```java
Response aResp = getAResponse();
// 其他代码
Response bResp = getBResponse();
return bResp;
```
- **异常处理**

**Bad**
```java
// 业务函数一般不抛异常，如果非要抛异常，一定不要抛Exception。
public void common()  throws Exception;

private String normal(String params){return params};
try{
		normal(params);
} catch (Exception e) {		 // 不需要捕捉异常
		e.printStackTrace();	// 捕捉异常后没有抛出业务异常, 除非你非常确定你在做什么
} finally {
		return "s";				// 不应该在finally中返回
}
```
**Good**
```java
private String normal(String params) throws IOException;
try {
		normal(params);
} catch (IOException e) {
		logger.error("io异常", e);
		throw new ServiceException("io异常", e);
}
return "s";
```
- **类，对象，方法**

**Bad**
```java
if (null != param) {
		boolean flag = service.findByParams(param);
		if (flag) {
				boolean secFlag = service.findByOther(param);
				if (secFlag) {
						// 业务内容
						return SuccessResponse();
				} else {
						return ErrorResponse();
				}
		} else {
				return ErrorResponse();
		}
} else {
		return ErrorResponse();
}
```
**Good** 注意不同逻辑代码之间的空行
```java
// 如果需要检验的参数较多,可以单独写一个check函数
if (param == null) {
		return ErrorResponse();		// 或者抛出serviceException, 看业务情况
}

boolean isExists = service.findByParams(params);
if (!isExists) {
		return ErrorResponse();
}

boolean isSuccess = service.findByOther(param);
if (!isSuccess) {
		return ErrorResponse();
}

// 正常业务代码
```
- **方法返回empty list**

**Bad**
```java
public List<Order> getOrders(){
		List<Order> orders = null;
		List<OrderDto> orderDtos = servier.find();
		if (orderDtos != null && orderDtos.size() > 0) {
			orders = new ArrayList<Order>();
			for (OrderDto dto : orderDtos) {
				Order order = new Order();
				orders.add(BeanUtils.copyPerties(dto, order));
			}
		}
		return orders;
}
```
**Good** 确保不返回null对象，不让调用该方法的人去判断是否为null
```java
public List<Order> getOrders(){
		List<Order> orders = Lists.newArrayList();
		List<OrderDto> orderDtos = servier.find();
		for (OrderDto dto : orderDtos) {
			Order order = new Order();
			orders.add(BeanUtils.copyPerties(dto, order));
		}
		return orders;
}
```
- **第三方类库**
	- guava
	- common-lang
	- common-io
	- common-bean
	- common-codec
	- common-collections
	- springutils
	- joda-time

常用方法:
```java
// guava新的集合类, BiMap, Table, MulitMap等
List<String> list = Lists.newArrayList();	// set, map类似

// apache-common
StringUtils.isBlank, notBlank, isEmpty, equals, join,split
IOUtils
BeanUtils
FileUtils
ColletionUtils
RandomUtils

//springutils
ClassPathResource
FileCopyUtils

// joda-time
DateTime dt = new DateTime();
```
