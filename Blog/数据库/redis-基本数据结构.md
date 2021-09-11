**【Redis】Redis的基本数据结构**



## 简介

> ​	**“Redis is an open source (BSD licensed), in-memory data structure store, used as a database, cache and message broker.”** —— Redis是一个开放源代码（BSD许可）的内存中数据结构存储，用作数据库，缓存和消息代理。 *(摘自官网)*

**优点**

- **异常快** - Redis 非常快，每秒可执行大约 110000 次的设置(SET)操作，每秒大约可执行 81000 次的读取/获取(GET)操作。
- **支持丰富的数据类型** - Redis 支持开发人员常用的大多数数据类型，例如列表，集合，排序集和散列等等。这使得 Redis 很容易被用来解决各种问题，因为我们知道哪些问题可以更好使用地哪些数据类型来处理解决。
- **操作具有原子性** - 所有 Redis 操作都是原子操作，这确保如果两个客户端并发访问，Redis 服务器能接收更新的值。
- **多实用工具** - Redis 是一个多实用工具，可用于多种用例，如：缓存，消息队列(Redis 本地支持发布/订阅)，应用程序中的任何短期数据，例如，web应用程序中的会话，网页命中计数等。

Redis 是一个键值对数据库（key-value DB）， 数据库的值可以是字符串、集合、列表等多种类型的对象， 而数据库的键则总是字符串对象。

## 基本使用

安装过程可以参考：[Redis 安装 | 菜鸟教程](https://www.runoob.com/redis/redis-install.html)

安装完之后， **src** 目录下会出现编译后的 redis 服务程序 redis-server，还有用于测试的客户端程序 redis-cli

redis-server用于开启redis服务器

```
./redis-server
```

也可以自定义配置文件

```
./redis-server ../redis.conf
```

redis-cli用于连接redis数据库

```
# ./redis-cli
redis> set foo bar
OK
```





## 数据结构

在redis中，主要有如下的数据结构：

- 动态字符串（**string**）
- 链表（**list**）
- 字典（**hash**）
- 集合（**set**）
- 有序集合（**zset**）





### 字符串

```C
/* Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. */
struct __attribute__ ((__packed__)) sdshdr5 {
    unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr8 {
    uint8_t len; /* used */
    uint8_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```



你会发现同样一组结构 Redis 使用泛型定义了好多次，**为什么不直接使用 int 类型呢？**

因为当字符串比较短的时候，len 和 alloc 可以使用 byte 和 short 来表示，**Redis 为了对内存做极致的优化，不同长度的字符串使用不同的结构体来表示。**



C字符串问题：

- **获取字符串长度为 O(N) 级别的操作** → 因为 C 不保存数组的长度，每次都需要遍历一遍整个数组；
- 不能很好的杜绝 **缓冲区溢出/内存泄漏** 的问题 → 跟上述问题原因一样，如果执行拼接 or 缩短字符串的操作，如果操作不当就很容易造成上述问题；
- C 字符串 **只能保存文本数据** → 因为 C 语言中的字符串必须符合某种编码（比如 ASCII），例如中间出现的 `'\0'` 可能会被判定为提前结束的字符串而识别不了；





### 列表

我们可以从源码的 `adlist.h/listNode` 来看到对其的定义：

```c
/* Node, List, and Iterator are the only data structures used currently. */

typedef struct listNode {
    struct listNode *prev;
    struct listNode *next;
    void *value;
} listNode;

typedef struct listIter {
    listNode *next;
    int direction;
} listIter;

typedef struct list {
    listNode *head;
    listNode *tail;
    void *(*dup)(void *ptr);
    void (*free)(void *ptr);
    int (*match)(void *ptr, void *key);
    unsigned long len;
} list;
```





### 字典

通过 **“数组 + 链表”** 的链地址法来解决部分 **哈希冲突**，同时这样的结构也吸收了两种不同数据结构的优点。源码定义如 `dict.h/dictht` 定义：

```c
typedef struct dictht {
    // 哈希表数组
    dictEntry **table;
    // 哈希表大小
    unsigned long size;
    // 哈希表大小掩码，用于计算索引值，总是等于 size - 1
    unsigned long sizemask;
    // 该哈希表已有节点的数量
    unsigned long used;
} dictht;

typedef struct dict {
    dictType *type;
    void *privdata;
    // 内部有两个 dictht 结构
    dictht ht[2];
    long rehashidx; /* rehashing not in progress if rehashidx == -1 */
    unsigned long iterators; /* number of iterators currently running */
} dict;
```



---

参考：

- [Redis(1)——5种基本数据结构 - 我没有三颗心脏的博客](https://www.wmyskxz.com/2020/02/28/redis-1-5-chong-ji-ben-shu-ju-jie-gou/)
- [Redis(2)——跳跃表 - 我没有三颗心脏的博客](https://www.wmyskxz.com/2020/02/29/redis-2-tiao-yue-biao/)
- [简单动态字符串 — Redis 设计与实现](https://redisbook.readthedocs.io/en/latest/internal-datastruct/sds.html)
- [【Redis源码分析】一个对SDSHDR5是否使用的疑问 - SegmentFault 思否](https://segmentfault.com/a/1190000017450295)
- [Redis 深度历险：核心原理与应用实践 (豆瓣)](https://book.douban.com/subject/30386804/)
- [Redis为什么用跳表而不用平衡树？](https://mp.weixin.qq.com/s?__biz=MzA4NTg1MjM0Mg==&mid=2657261425&idx=1&sn=d840079ea35875a8c8e02d9b3e44cf95&scene=21#wechat_redirect)

