## 阅读java字节码

不熟悉java 字节码结构可以先查看[JAVA CLASS的文件结构](https://coolshell.cn/articles/9229.html)

详细的字节码解析程序[java 字节码解析](https://github.com/MrYang/java-class-parser)

一个简单的java 源文件

```java
package com.zz;

public class ReadClass {

    private int a;
    private String b = "2";

    public static void main(String[] args) {
        int r = new ReadClass().add();
        System.out.println(r);
    }
    
    private int add(){
        return a + Integer.valueOf(b);
    }

}
```

编译产生ReadClass.class 文件，使用`javap -verbose ReadClass`即可查看反编译字节码内容

完整输出内容为:

```java
警告: 二进制文件ReadClass包含com.zz.ReadClass
Classfile target/classes/com/zz/ReadClass.class
  Last modified 2017-9-8; size 829 bytes
  MD5 checksum 4baa5f736736a4406ce81f244070b91a
  Compiled from "ReadClass.java"
public class com.zz.ReadClass
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #12.#33        // java/lang/Object."<init>":()V
   #2 = String             #34            // 2
   #3 = Fieldref           #4.#35         // com/zz/ReadClass.b:Ljava/lang/String;
   #4 = Class              #36            // com/zz/ReadClass
   #5 = Methodref          #4.#33         // com/zz/ReadClass."<init>":()V
   #6 = Methodref          #4.#37         // com/zz/ReadClass.add:()I
   #7 = Fieldref           #38.#39        // java/lang/System.out:Ljava/io/PrintStream;
   #8 = Methodref          #40.#41        // java/io/PrintStream.println:(I)V
   #9 = Fieldref           #4.#42         // com/zz/ReadClass.a:I
  #10 = Methodref          #43.#44        // java/lang/Integer.valueOf:(Ljava/lang/String;)Ljava/lang/Integer;
  #11 = Methodref          #43.#45        // java/lang/Integer.intValue:()I
  #12 = Class              #46            // java/lang/Object
  #13 = Utf8               a
  #14 = Utf8               I
  #15 = Utf8               b
  #16 = Utf8               Ljava/lang/String;
  #17 = Utf8               <init>
  #18 = Utf8               ()V
  #19 = Utf8               Code
  #20 = Utf8               LineNumberTable
  #21 = Utf8               LocalVariableTable
  #22 = Utf8               this
  #23 = Utf8               Lcom/zz/ReadClass;
  #24 = Utf8               main
  #25 = Utf8               ([Ljava/lang/String;)V
  #26 = Utf8               args
  #27 = Utf8               [Ljava/lang/String;
  #28 = Utf8               r
  #29 = Utf8               add
  #30 = Utf8               ()I
  #31 = Utf8               SourceFile
  #32 = Utf8               ReadClass.java
  #33 = NameAndType        #17:#18        // "<init>":()V
  #34 = Utf8               2
  #35 = NameAndType        #15:#16        // b:Ljava/lang/String;
  #36 = Utf8               com/zz/ReadClass
  #37 = NameAndType        #29:#30        // add:()I
  #38 = Class              #47            // java/lang/System
  #39 = NameAndType        #48:#49        // out:Ljava/io/PrintStream;
  #40 = Class              #50            // java/io/PrintStream
  #41 = NameAndType        #51:#52        // println:(I)V
  #42 = NameAndType        #13:#14        // a:I
  #43 = Class              #53            // java/lang/Integer
  #44 = NameAndType        #54:#55        // valueOf:(Ljava/lang/String;)Ljava/lang/Integer;
  #45 = NameAndType        #56:#30        // intValue:()I
  #46 = Utf8               java/lang/Object
  #47 = Utf8               java/lang/System
  #48 = Utf8               out
  #49 = Utf8               Ljava/io/PrintStream;
  #50 = Utf8               java/io/PrintStream
  #51 = Utf8               println
  #52 = Utf8               (I)V
  #53 = Utf8               java/lang/Integer
  #54 = Utf8               valueOf
  #55 = Utf8               (Ljava/lang/String;)Ljava/lang/Integer;
  #56 = Utf8               intValue
{
  public com.zz.ReadClass();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=2, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: ldc           #2                  // String 2
         7: putfield      #3                  // Field b:Ljava/lang/String;
        10: return
      LineNumberTable:
        line 3: 0
        line 6: 4
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      11     0  this   Lcom/zz/ReadClass;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=2, args_size=1
         0: new           #4                  // class com/zz/ReadClass
         3: dup
         4: invokespecial #5                  // Method "<init>":()V
         7: invokespecial #6                  // Method add:()I
        10: istore_1
        11: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
        14: iload_1
        15: invokevirtual #8                  // Method java/io/PrintStream.println:(I)V
        18: return
      LineNumberTable:
        line 9: 0
        line 10: 11
        line 11: 18
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      19     0  args   [Ljava/lang/String;
           11       8     1     r   I
}
SourceFile: "ReadClass.java"
```

下面解释一下字节码内容：

首先前面8行描述了这个class文件大小，修改时间，md5，版本号等基本信息

接下来是常量池：
第一列是常量位置，类型，第二列表示常量组成或者具体值，`//`后面是注释

第一个常量为Methodref类型，具体信息由第12，33个常量组成，然后继续查看第12个常量是Class类型，具体信息是在46常量里面，46常量为字符串"java/lang/Object"，33常量为NameAndType类型，由17，18常量组成，以此类推，最后第一个常量的具体值为`java/lang/Object."<init>":()V`

后面的常量以此类推即可得到所有常量的值

再之后是构造函数
`public com.zz.ReadClass()` 方法名
`descriptor: ()V` 方法描述符，要解释的话又要一大堆，可以参见[这篇文章](http://blog.csdn.net/zhangjg_blog/article/details/21487287)
`flags: ACC_PUBLIC` 访问权限，表示public

`Code` code属性，代码编译完后就是它了

```java
stack=2, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: aload_0
         5: ldc           #2                  // String 2
         7: putfield      #3                  // Field b:Ljava/lang/String;
        10: return
      LineNumberTable:
        line 3: 0
        line 6: 4
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      11     0  this   Lcom/zz/ReadClass;
```

Java字节码指令参见[这里](http://gityuan.com/2015/10/24/jvm-bytecode-grammar/)

`stack=2, locals=1, args_size=1` 表示有两层栈, 一个参数(既this),示例方法默认都自带this参数，静态方法没有this参数
`aload_0` 装载第一个引用类型进栈，既类自身 (this)
`invokespecial #1` 调用方法, `#1` 为第一个常量, 既Object的初始方法（继承了Object类)
`ldc #2` 把第二个常量推送至栈顶
`putfield #3` 给第三个常量设值, 既 `this.b = "2"` b为String类型 


`LineNumberTable` 行号表属性：第3行源代码对应第0条指令既`0: aload_0`, 第6行源代码对应第4条指令`aload_0`, 这里没啥意义，因为是默认的构造函数，没有具体的代码
`LocalVariableTable` 局部变量表属性: `this`的生命周期从第0条指令开始，长度有11个长度这么长，到方法结束

```java
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=2, args_size=1
         0: new           #4                  // class com/zz/ReadClass
         3: dup
         4: invokespecial #5                  // Method "<init>":()V
         7: invokespecial #6                  // Method add:()I
        10: istore_1
        11: getstatic     #7                  // Field java/lang/System.out:Ljava/io/PrintStream;
        14: iload_1
        15: invokevirtual #8                  // Method java/io/PrintStream.println:(I)V
        18: return
      LineNumberTable:
        line 9: 0
        line 10: 11
        line 11: 18
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      19     0  args   [Ljava/lang/String;
           11       8     1     r   I
```

`stack=2, locals=1, args_size=1` 表示有两层栈, 一个参数(args),静态方法没有this参数

参考java 字节码指令大概可以各个指令含义

1. new ReadClass
2. 调用init 方法
3. 调用add(第6个常量) 方法
4. 把add方法的结果存储到第一个变量中
5. 获取System.out 静态变量
6. 装载第一个变量，既add方法存储的变量
7. 调用println 方法打印

`LineNumberTable`, `LocalVariableTable` 同上处理