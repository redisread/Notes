

### 命令

1. shell中执行一个字符串命令

   ```shell
   cmd="echo hello world"
   $cmd	# way1
   eval $cmd	# way2
   ```

2. shell下单引号、双引号、反引号的区别

   - 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
   - 双引号里可以有变量，双引号可以出现转义字符
   - shell命令用反引号可以进行命令替换，是指shell能够将一个命令的标准输出插在一个命令行中任何位置。

3. sed命令对某一行进行操作

   https://blog.csdn.net/xj626852095/article/details/26101273

   ```
       y      在使用v模式选定了某一块的时候，复制选定块到缓冲区用；
       yy    复制整行（nyy或者yny ，复制n行，n为数字）；
       y^   复制当前到行头的内容；
       y$    复制当前到行尾的内容；
       yw   复制一个word （nyw或者ynw，复制n个word，n为数字）；
       yG    复制至档尾（nyG或者ynG，复制到第n行，例如1yG或者y1G，复制到档尾） 
   ```

4. 在vim中取消搜索的高亮

   ```bash
   :noh
   ```

5. vi/vim复制粘贴命令

   https://blog.csdn.net/lanxinju/article/details/5727262

6. 查询centos版本

   ```
   cat /etc/redhat-release
   ```

7. 解压tar.gz文件

   ```
   tar -zxvf ×××.tar.gz
   ```

8. 解压tar.bz2文件

   ```
   tar -jxvf ×××.tar.bz2
   ```

9. 刷新host

   ```
   /etc/init.d/network restart
   ```

10. 命令行对文件的某一行做处理

    1. 在指定行插入或追加: a, i
       a. 在test.txt第一行前插入：sed “1 i This is a test file” test.txt
       b. 在test.txt最后一行追加：sed “$ a This is the end of file” test.txt
    2. 删除： d
       a. 删除test.txt第二行： sed ‘2d’ test.txt
       b. 删除test.txt符合正则表达式`/fish`的行： sed ‘/fish/d’ test.txt

    https://blog.csdn.net/hs_err_log/article/details/79646376

11. shell中判断是centos7还是8、6

    ```shell
    v=`cat /etc/redhat-release|sed -r 's/.* ([0-9]+)\..*/\1/'`
    if [ $v -eq 6 ]; then
     	yum install scons -y
    fi
    if [ $v -eq 7 ]; then
     	yum install scons -y
    fi
    ```

12. shell 中的条件判断 “并且” “或者”  https://blog.csdn.net/lanyang123456/article/details/57416906

    or

    ```shell
    if [ c1 -o c2 ]; then
    …
    fi
    if [ c1 ] || [  c2 ]; then
    …
    fi
    ```

    and

    ```shell
    if [ c1 -a c2 ]; then
    …
    fi
    if [ c1 ] && [ c2 ]; then
    …
    fi
    ```

    [数字判断](https://blog.csdn.net/ithomer/article/details/5904632)

    ```
    int1 -eq int2　　　　两数相等为真
    int1 -ne int2　　　　两数不等为真
    int1 -gt int2　　　　int1大于int2为真
    int1 -ge int2　　　　int1大于等于int2为真
    int1 -lt int2　　　　int1小于int2为真
    int1 -le int2　　　　int1小于等于int2为真
    ```

13. 判断一个文件是否存在

    ```shell
    # 这里的-f参数判断$myFile是否存在
    if [ ! -f "$myFile" ]; then
    touch "$myFile"
    fi
    ```

    https://www.cnblogs.com/sunyubo/archive/2011/10/17/2282047.html

14. Apache httpd网页位置

    ```
    /var/www/html
    ```

15. Apache httpd配置文件

    ```
    /etc/httpd/conf/httpd.conf
    ```

16. Shell函数定义语法

    ```shell
    [ function ] funname [()]
    {
        action;
        [return int;]
    }
    ```

17. 在 Shell 脚本中调用另一个 Shell 脚本的三种方式

    ```
    fork: 如果脚本有执行权限的话，path/to/foo.sh。如果没有，sh path/to/foo.sh。
    exec: exec path/to/foo.sh
    source: source path/to/foo.sh
    ```

18. 刷新DNS

    ```
    yum install -y nscd
    systemctl restart nscd
    ```

    

19. 获取本机外网IP

    ```
    curl http://members.3322.org/dyndns/getip
    ```

20. 安装使用locate命令

    ```
    yum install mlocate -y
    locate libc.a
    ```

21. 











### 命令行功夫

1. `.`操作可以重复上次执行的操作。
2. `>G` 命令会增加从当前行到文档末尾处的缩进层级。
3. 

