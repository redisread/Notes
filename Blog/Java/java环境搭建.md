



### java编译

```
javac Test.java
```



### java执行

```
java Test
```

java后面接的不需要加`.class`后缀的原因是java执行程序会去寻找名字为Test的类，所以不需要加`.class`的后缀。



只要有一个类，每一个类都会生成一个单独的`.class`文件。

```java
public class Test{
	public static void main(String[] args) {
		System.out.println("Hello 你好");
	}
}

class Dog{
}
```

![image-20210513173532055](https://i.loli.net/2021/05/13/xWcu3ZwdBmjKpDM.png)



使用非public类执行其他类的入口函数：

```java
public class Test{
	public static void main(String[] args) {
		System.out.println("Hello 你好");
	}
}

class Dog{
	public static void main(String[] args) {
		System.out.println("你好，Dog");
	}
}

```

执行

```
java Test.java
Javac Dog
```

![image-20210513174214422](../../../AppData/Roaming/Typora/typora-user-images/image-20210513174214422.png)







### 如何学习Java

> （可以应用到其他技术去学习）
>

如何快速学习一个技术：

需求：

1. 工作中需要。
2. 换工作（对方要求需要这种技术）。
3. 技术控（紧跟形势）

> 如何找需求
>





看看能否使用传统技术解决？对认识新的框架很有用。

传统技术-》新技术

1. 能解决，但是不完美
2. 解决不了





引出我们需要学习的新技术和知识点



学习新知识和新技术的基本语句和基本语法（不要考虑细节）（先有一个全局观）



快速入门（基本程序，crud）



开始研究技术的注意事项、使用细节、使用规范、如何规范。（永远可以进行）

![学习过程](https://i.loli.net/2021/05/13/ue4dkTSQ7rMCEXP.png)

