# 简单Java





一个简单的java程序`test.java`:

```java
public class test {
    public static void main(String[] args) {
        System.out.println("Hello,world");
    }
}
```



![java](https://i.loli.net/2021/04/29/uIozlCbqfdpxU8F.jpg)



public class的类名需要和代码文件名一致



每一个java程序入口地址是main函数

```java
 public static void main(String[] args)
```









修饰符：

- 访问控制修饰符 : default, public , protected, private

  对于一个Class的成员变量或成员函数，如果不用public, protected, private中的任何一个修饰，那么该成员获得“默认访问控制”级别，即package access （包访问）。属于package access的成员可以被同一个包中的其他类访问，但不能被其他包的类访问。访问级别保护的强度：public<protected<默认<private

- 非访问控制修饰符 : final, abstract, static, synchronized



![java解释器](https://i.loli.net/2021/04/29/fcRW4EmZaiXuFvx.png)





---

参考：

1. [Java成员的默认访问控制_Yerasel的专栏-CSDN博客_java中默认访问控制方式](https://blog.csdn.net/ozwarld/article/details/7718140)
2. [Java中public，protected，private以及默认的访问权限作用域_一只不开窍的笨鸟-CSDN博客_java private作用域](https://blog.csdn.net/qq_31686787/article/details/52553974)
3. [Java的类名与文件名必须一致_shaoxiaoning的专栏-CSDN博客](https://blog.csdn.net/shaoxiaoning/article/details/40424087)