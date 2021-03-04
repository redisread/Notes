# Python  Note

## 命名

常量名所有字母大写，由下划线连接各个单词；变量名全部小写，由下划线连接各个单词

```python
WHITE = 0XFFFFFF
THIS_IS_A_CONSTANT = 1

color = WHITE
this_is_a_variable = 1
```







## 字符串

### 格式化format

[Python format 格式化函数 | 菜鸟教程](https://www.runoob.com/python/att-string-format.html)

```python
"{} {}".format("hello", "world")    # 不设置指定位置，按默认顺序
```



## 模块

### 导入模块



## 注释



## 类

根据类的字符串名实例化对象

[python 根据类的字符串名实例化对象_youbo_sun的专栏-CSDN博客_python根据类名实时生成对象](https://blog.csdn.net/sun754276603/article/details/49533157)

```python
class obj(object): 
   pass 
 a = eval('obj()')
```



## 规范参考

[Python PEP8 编码规范中文版_基因记忆-CSDN博客_pep8 python 编码规范](https://blog.csdn.net/ratsniper/article/details/78954852)



### 参数传递方式

[Python中参数传递方式 - 简书](https://www.jianshu.com/p/8d242b2b4f2e)



## 模块使用

### argparse模块

参考：

1. [argparse模块用法实例详解](https://zhuanlan.zhihu.com/p/56922793)
2. [Python 命令行之旅：深入 argparse（二） - 削微寒 - 博客园](https://www.cnblogs.com/xueweihan/p/11415703.html)

创建ArgumentParser 对象解释器

```python
parser = argparse.ArgumentParser(
                        description=DESDCIPTION,epilog=EPILOG, prog="Invoker")
```

[Python argparse：如何在帮助文本中插入换行符？ - ITranslater](https://www.itranslater.com/qa/details/2112192355905307648)

[python---argparse 解析 bool 值 | Python 技术论坛](https://learnku.com/articles/44443)

常用功能：

- 子命令





## tips

- 遵从Python语言惯例，即社区在使用Python语言时约定俗成的行为习惯

- 实例方法的第一个参数必须为self,类方法的第一个参数必须为cls

- 所有的 Python 脚本文件都应在文件头标上`# -*- coding:utf-8 -*-`

- “:”用在行尾时前后皆不加空格，如分枝、循环、函数和类定义语言；用在非行尾时两端加空格，如

  ```python
  d = {'key' :  'value'}
  ```

- 导入自定义py文件

  将自定义模块打包：

  将一揽子的模块（.py文件）放在一个文件夹里面，再添加一个`__init__.py`，这样这个文件夹就成为了一个包。可以将这个包放入python安装目录的`../Lib/site-packages/`中，这样就可以导入这个包中的模块使用了

- 对你的代码运行pylint

- 每行不超过80个字符

- 不要使用反斜杠连接行.

  Python会将 圆括号, 中括号和花括号中的行隐式的连接起来 , 你可以利用这个特点. 如果需要, 你可以在表达式外围增加一对额外的圆括号.

- 需要在函数内部修改全局变量，需要先使用global关键字进行声明：

  ```python
  global x
  x = 2
  ```

- 



