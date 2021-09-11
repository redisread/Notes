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



#### 访问文件树

使用autoindex关键字

```nginx
    server {
        listen       8090;
        server_name  jiahongw.com www.jiahongw.com;

        location / {
            root /mnt/;
            autoindex on;
           #index  index.html index.htm;
        }
    }
```



![image-20210205141227372](https://raw.githubusercontent.com/redisread/Image/master/Windowsimage-20210205141227372.png)





### Action

[GitHub Actions 入门教程 - P3TERX ZONE](https://p3terx.com/archives/github-actions-started-tutorial.html)

必须将工作流程文件存储在仓库的 `.github/workflows` 目录中。

![图片1](https://raw.githubusercontent.com/redisread/Image/master/WindowsYamlExample1.png)

**空格/缩进**

例如，这是正确的：

```
Key: Value
```

但这将失败：

```
Key:Value
```

   ^^冒号后没有空格！



**Env**

Env定义了环境变量的映射，这些变量可用于工作流中的所有作业和步骤。您还可以设置仅对作业或步骤可用的环境变量。





部署自己的服务器

[使用 GitHub Actions 实现博客自动化部署 | Frost's Blog](https://frostming.com/2020/04-26/github-actions-deploy/)





SSH 远程执行任务

[SSH 远程执行任务 - sparkdev - 博客园](https://www.cnblogs.com/sparkdev/p/6842805.html)

[静态网站生成工具-Hugo | 小木](https://xinxiamu.github.io/2020/06/29/hugo-start/)



```
git_build

```

