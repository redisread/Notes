unix为什么强大？

1. 仅提供几百个系统调用并且有非常明确的设计目的。
2. 所有的东西都是文件。
3. 系统工具软件使用C语言编写。可移植与高性能。
4. 进程创建非常迅速，有独特的fork系统调用。
5. 提供简单稳定的进程间通信元语。

> 快速简洁的进程创建使Unix吧目标放在一次执行保质保量的完成一个任务，而简单稳定的进程间通信机制又可以保证这些单一目的的简单程序可以方便的组合在一起。，去解决更复杂的问题。



Linux与Unix的区别：

Linux是类Unix系统，但不是Unix系统。Linux没有像其他Unix变种那样直接使用Unix的源代码，它的实可能与其他Unix的大相径庭，但是Linux没有抛弃Unix的设计目标并且保证了应用程序编程接口的一致。

并且Linux是一个非商业化的产品。



### Git初始操作

命令行指令

Git 全局设置

```
git config --global user.name "吴嘉鸿13732"
git config --global user.email "13732@sangfor.com"
```

创建新版本库

```
git clone git@mq.code.sangfor.org:13732/subhealth_mem_detector.git
cd subhealth_mem_detector
touch README.md
git add README.md
git commit -m "add README"
git push -u origin master
```

已存在的文件夹

```
cd existing_folder
git init
git remote add origin git@mq.code.sangfor.org:13732/subhealth_mem_detector.git
git add .
git commit -m "Initial commit"
git push -u origin master
```

已存在的 Git 版本库

```
cd existing_repo
git remote rename origin old-origin
git remote add origin git@mq.code.sangfor.org:13732/subhealth_mem_detector.git
git push -u origin --all
git push -u origin --tags
```

> 注意，git仓库上只存公钥



github

```
git remote add origin https://github.com/user/repo.git
```



### Nginx

主要功能：

1. 静态资源
2. 反向代理
3. API服务

![image-20210201173651090](https://i.loli.net/2021/02/01/cbQafGDUpSRuVqw.png)

配置语法

![image-20210201172833071](https://i.loli.net/2021/02/01/4Ym7ZLzJ3euqcrt.png)





Nginx中的upstream(负载均衡)

https://www.cnblogs.com/wangjiachen666/p/11465897.html

基本配置

首先在`nginx.conf`配置文件中的`http`节点内增加`upstream`节点

```
http {
	# ...
	upstream demo {
        server 127.0.0.1:9000;
        server 127.0.0.1:9002;
    }
    # ...
}
```

然后我们在`server`节点中配置反向代理，让前端的请求能够代理到上面配置的负载机上，如下：

```
server {
	# ...
	location / {
    	root   html;
    	index  index.html index.htm;
    	proxy_pass http://demo;
    }
    # ...
}
```

注意：这里的`proxy_pass`配置应为`http://`+`upstream`名称。



Nginx查看版本：

```
nginx -V
```



热重载配置文件

```
nginx -s reload
```

检查配置文件是否正确

```
nginx -t
```

指定配置文件:-c



全教程：[https://segmentfault.com/a/1190000022673232](https://segmentfault.com/a/1190000022673232)







