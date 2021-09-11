## 开篇

### 操作系统是什么？

![下载](https://i.loli.net/2021/03/29/MJFBEtl3PowkUn1.jpg)



![进程](https://i.loli.net/2021/03/29/QhNcY3LR8saXG2H.jpg)



### 一些问题

- 操作系统实模式
- 0号进程、1号进程以及2号进程之间的关系
- 触发系统调用的指令有哪些
- ![image-20210329144859716](https://i.loli.net/2021/03/29/QR91B2KTeDPlO5n.png)
- 



### 学习路径

我总结了一下，在整个 Linux 的学习过程中，要爬的坡有六个，分别是：熟练使用 Linux 命令行、使用 Linux 进行程序设计、了解 Linux 内核机制、阅读 Linux 内核代码、实验定制 Linux 组件，以及最后落到生产实践上。以下是我为你准备的爬坡秘籍以及辅助的书单弹药。

![image-20210329150408989](https://i.loli.net/2021/03/29/Imo6RHFydCgXNQr.png)







![实例](https://i.loli.net/2021/03/29/GRsvhlY6WDzjuBf.png)

![下载 ](https://i.loli.net/2021/03/29/Zaur7mIdAEpbQGq.jpg)



在 Linux 下面，二进制的程序也要有严格的格式，这个格式我们称为**ELF**（Executeable and Linkable Format，可执行与可链接格式）。这个格式可以根据编译的结果不同，分为不同的格式。





## 内存管理

### 什么是内存管理

​	一个内存管理系统至少应该做三件事情：

- 第一，虚拟内存空间的管理，每个进程看到的是独立的、互不干扰的虚拟地址空间；
- 第二，物理内存的管理，物理内存地址只有内存管理模块能够使用；
- 第三，内存映射，需要将虚拟内存和物理内存映射、关联起来。



### 虚拟内存

作进程之间数据的隔离，防止两个进程访问同一个物理地址，进程不能直接访问物理地址。，指令写入的地址都是虚拟地址。

当程序要访问虚拟地址的时候，由内核的数据结构进行转换，转换成不同的物理地址，这样不同的进程运行的时候，写入的是不同的物理地址，这样就不会冲突了。



### 段内存管理



段的引入：

那 16 的位的寄存器如何能访问 20 位的地址？

2 的16 次方如果直着来如何能访问到 2 的 20 次方所表达的数？

直着来是不可能的，因此就需要操作一下。

也就是引入段的概念，让 CPU 通过**「段基地址+段内偏移」**来访问内存。

所以再具体一点的计算规则其实是：段基地址左移 4 位（就是乘16）再加上段内偏移，这样得到的就是 20 位的地址。

比如现在的要访问的内存地址是0x05808，那么段基地址可以是 0x0580，偏移量就是 0x0008。

![img](https://i.loli.net/2021/04/02/t96vKGlaZMXePq4.jpg)

这样内存的寻址空间就扩大到 20 位了。

对了，专门为分段而生的寄存器为段寄存器，当时里面直接存放段基地址。

不过渐渐地人们就考虑到安全问题，因为在这个时候程序之间的地址没有隔离，我的程序可以访问你的程序地址，这就很不安全。

于是，就有了**保护模式**。

其实就是 CPU 在访问地址的时候做了约束，会判断地址是否在允许的范围内，会判断当前的程序对目的地址是否有访问权限。

搞了个 GDT （全局描述符表）存放所有段描述符

![image-20210402171551648](https://i.loli.net/2021/04/02/6ILCZrvi8xBq7WP.png)

段寄存器里面也不是直接放段基地址了，而是放了一个叫**选择子**的东西。

大致可以认为就是段描述符的索引，也就是通过这个索引去找到段描述符，所以叫选择子。

这个选择子里面还有一点属性。

![image-20210402171631966](https://i.loli.net/2021/04/02/obPBFEmw4VhOzT7.png)

当地址访问时，如果 RPL 的权限低于目标特权级（DPL）时，就会拒绝访问，于是就起到了保护的作用。

所以称之为**保护模式**，之前的那种没有判断权限的称之为**实模式。**

![image-20210402171711095](https://i.loli.net/2021/04/02/B9E1i6uqXozwkvU.png)





参考：[内存分页不就够了？为什么还要分段？还有段页式？ - 知乎](https://zhuanlan.zhihu.com/p/341861276)

*存在碎片*

分段机制下的虚拟地址由两部分组成，**段选择子**和**段内偏移量**。段选择子就保存在咱们前面讲过的段寄存器里面。段选择子里面最重要的是**段号**，用作段表的索引。段表里面保存的是这个段的**基地址**、**段的界限**和**特权等级**等。虚拟地址中的段内偏移量应该位于 0 和段界限之间。如果段内偏移量是合法的，就将段基地址加上段内偏移量得到物理内存地址。

![段内存管理](https://i.loli.net/2021/04/02/8WvCXUzuOtoDI4w.png)



在 Linux 里面，段表全称**段描述符表**（segment descriptors），放在**全局描述符表 GDT**（Global Descriptor Table）里面，会有下面的宏来初始化段描述符表里面的表项。

```c
#define GDT_ENTRY_INIT(flags, base, limit) { { { \
		.a = ((limit) & 0xffff) | (((base) & 0xffff) << 16), \
		.b = (((base) & 0xff0000) >> 16) | (((flags) & 0xf0ff) << 8) | \
			((limit) & 0xf0000) | ((base) & 0xff000000), \
	} } }
```



其实 Linux 倾向于另外一种从虚拟地址到物理地址的转换方式，称为**分页**（Paging）。

### 页内存管理

虚拟地址分为两部分，**页号**和**页内偏移**。页号作为页表的索引，页表包含物理页每页所在物理内存的基地址。这个基地址与页内偏移的组合就形成了物理内存地址。

![image-20210402164748687](https://i.loli.net/2021/04/02/2WjHX5eTDx9G3Bg.png)

虚拟内存中的页通过页表映射为了物理内存中的页

![image-20210402164847309](https://i.loli.net/2021/04/02/uaiV6Q9bspn58rd.png)



多级页表

![image-20210402165215406](https://i.loli.net/2021/04/02/3v8xBXCo5nIATWP.png)

当然对于 64 位的系统，两级肯定不够了，就变成了四级目录，分别是**全局页目录项 PGD（Page Global Directory）**、**上层页目录项 PUD（Page Upper Directory）**、**中间页目录项 PMD（Page Middle Directory）**和**页表项 PTE（Page Table Entry）**。

![image-20210402165319998](https://i.loli.net/2021/04/02/jWMudkq1tSaDylC.png)



总结：

我们可以把内存管理系统精细化为下面三件事情：

- 第一，虚拟内存空间的管理，将虚拟内存分成大小相等的页；
- 第二，物理内存的管理，将物理内存分成大小相等的页；
- 第三，内存映射，将虚拟内存也和物理内存也映射起来，并且在内存紧张的时候可以换出到硬盘中。

![image-20210402165434112](https://i.loli.net/2021/04/02/B1l8dSftqVNcZ4z.png)



> 分页机制本质上来说就是类似于linux文件系统的目录管理一样，页目录项和页表项相当于根目录和上级目录，页内便宜量就是相对路径，绝对路径就是整个32位地址，分布式存储系统也是采用的类似的机制，先用元数据存储前面的路径，再用块内偏移定位到具体文件，感觉道理都差不多
>
> ——Leon



### 为什么使用页管理而不使用段管理呢

内存碎片

获取页面大小：

![image-20210402165817026](https://i.loli.net/2021/04/02/ZMYO4LXST5G1HeD.png)

![image-20210402170126661](https://i.loli.net/2021/04/02/PCxNA7BrMILmHZ6.png)

大页内存：

http://blog.itpub.net/29291882/viewspace-2133990/

[程序性能优化提升方法--大页内存_十里平湖的博客-CSDN博客](https://blog.csdn.net/qq_36357820/article/details/78922819)





### 进程的虚拟内存空间

32位和64位的虚拟内存空间布局

![image-20210402173500251](https://i.loli.net/2021/04/02/y8hU9YAi2MGTzto.png)

用户态布局

用户态虚拟空间里面有几类数据，例如代码、全局变量、堆、栈、内存映射区等。在 struct mm_struct 里面，有下面这些变量定义了这些区域的统计信息和位置。

```
unsigned long mmap_base;	/* base of mmap area */
unsigned long total_vm;		/* Total pages mapped */
unsigned long locked_vm;	/* Pages that have PG_mlocked set */
unsigned long pinned_vm;	/* Refcount permanently increased */
unsigned long data_vm;		/* VM_WRITE & ~VM_SHARED & ~VM_STACK */
unsigned long exec_vm;		/* VM_EXEC & ~VM_WRITE & ~VM_STACK */
unsigned long stack_vm;		/* VM_STACK */
unsigned long start_code, end_code, start_data, end_data;
unsigned long start_brk, brk, start_stack;
unsigned long arg_start, arg_end, env_start, env_end;
```



解释：

- mmap_base 表示虚拟地址空间中用于内存映射的起始地址。
- total_vm 是总共映射的页的数目
- data_vm 是存放数据的页的数目，exec_vm 是存放可执行文件的页的数目，stack_vm 是栈所占的页的数目。
- start_code 和 end_code 表示可执行代码的开始和结束位置，start_data 和 end_data 表示已初始化数据的开始位置和结束位置。
- start_brk 是堆的起始位置，brk 是堆当前的结束位置。
- start_stack 是栈的起始位置，栈的结束位置在寄存器的栈顶指针中。
- arg_start 和 arg_end 是参数列表的位置， env_start 和 env_end 是环境变量的位置。它们都位于栈中最高地址的地方。



![内存布局](https://i.loli.net/2021/04/02/gPoVSMEA2rJ3snk.png)

除了位置信息之外，struct mm_struct 里面还专门有一个结构 vm_area_struct，来描述这些区域的属性。

```
struct vm_area_struct *mmap;		/* list of VMAs */
struct rb_root mm_rb;
```

这里面一个是单链表，用于将这些区域串起来。另外还有一个红黑树。又是这个数据结构，在进程调度的时候我们用的也是红黑树。它的好处就是查找和修改都很快。这里用红黑树，就是为了快速查找一个内存区域，并在需要改变的时候，能够快速修改。

```
struct vm_area_struct {
	/* The first cache line has the info for VMA tree walking. */
	unsigned long vm_start;		/* Our start address within vm_mm. */
	unsigned long vm_end;		/* The first byte after our end address within vm_mm. */
	/* linked list of VM areas per task, sorted by address */
	struct vm_area_struct *vm_next, *vm_prev;
	struct rb_node vm_rb;
	struct mm_struct *vm_mm;	/* The address space we belong to. */
	struct list_head anon_vma_chain; /* Serialized by mmap_sem &
					  * page_table_lock */
	struct anon_vma *anon_vma;	/* Serialized by page_table_lock */
	/* Function pointers to deal with this struct. */
	const struct vm_operations_struct *vm_ops;
	struct file * vm_file;		/* File we map to (can be NULL). */
	void * vm_private_data;		/* was vm_pte (shared mem) */
} __randomize_layout;
```

vm_start 和 vm_end 指定了该区域在用户空间中的起始和结束地址。vm_next 和 vm_prev 将这个区域串在链表上。vm_rb 将这个区域放在红黑树上。vm_ops 里面是对这个内存区域可以做的操作的定义。

虚拟内存区域可以映射到物理内存，也可以映射到文件，映射到物理内存的时候称为匿名映射，anon_vma 中，anoy 就是 anonymous，匿名的意思，映射到文件就需要有 vm_file 指定被映射的文件。

![下载](https://i.loli.net/2021/04/02/5QSC3ylkX7OEKmI.jpg)





内核态布局

我们来看 32 位的内核态的布局。

![image-20210402235909737](https://i.loli.net/2021/04/02/t9DucBrkF8oHWL3.png)

32 位的内核态虚拟地址空间一共就 1G，占绝大部分的前 896M，我们称为**直接映射区**。

所谓的直接映射区，就是这一块空间是连续的，和物理内存是非常简单的映射关系，其实就是虚拟内存地址减去 3G，就得到物理内存的位置。



64 位的内核布局

![下载 (1)](https://i.loli.net/2021/04/03/aHntDRbjGJrUwAS.jpg)





用户态：

- 代码段、全局变量、BSS
- 函数栈
- 堆
- 内存映射区

内核态：

- 内核的代码、全局变量、BSS
- 内核数据结构例如 task_struct
- 内核栈
- 内核中动态分配的内存





总结一下进程运行状态在 32 位下对应关系

![32位](https://i.loli.net/2021/04/03/3SqC7WAUoFGdQin.jpg)

 64 位的对应关系

![64位](https://i.loli.net/2021/04/03/bftuzjWgZh2MO45.jpg)





### 物理页面管理

由于物理地址是连续的，页也是连续的，每个页大小也是一样的。因而对于任何一个地址，只要直接除一下每页的大小，很容易直接算出在哪一页。每个页有一个结构 struct page 表示，这个结构也是放在一个数组里面，这样根据页号，很容易通过下标找到相应的 struct page 结构。



如果是这样，整个物理内存的布局就非常简单、易管理，这就是最经典的**平坦内存模型**（Flat Memory Model）。

我们讲 x86 的工作模式的时候，讲过 CPU 是通过总线去访问内存的，这就是最经典的内存使用方式。

![image-20210403170724466](https://i.loli.net/2021/04/03/Areya3SCKBcqu9V.png)

在这种模式下，CPU 也会有多个，在总线的一侧。所有的内存条组成一大片内存，在总线的另一侧，所有的 CPU 访问内存都要过总线，而且距离都是一样的，这种模式称为**SMP**（Symmetric multiprocessing），即对称多处理器。当然，它也有一个显著的缺点，就是总线会成为瓶颈，因为数据都要走它。



为了提高性能和可扩展性，后来有了一种更高级的模式，**NUMA**（Non-uniform memory access），非一致内存访问。在这种模式下，内存不是一整块。每个 CPU 都有自己的本地内存，CPU 访问本地内存不用过总线，因而速度要快很多，每个 CPU 和内存在一起，称为一个 NUMA 节点。但是，在本地内存不足的情况下，每个 CPU 都可以去另外的 NUMA 节点申请内存，这个时候访问延时就会比较长。

![image-20210403170757345](https://i.loli.net/2021/04/03/QMElovUSkbq56Gx.png)

后来内存技术牛了，可以支持热插拔了。这个时候，不连续成为常态，于是就有了稀疏内存模型。

#### 节点

我们首先要能够表示 NUMA 节点的概念，于是有了下面这个结构 typedef struct pglist_data pg_data_t，它里面有以下的成员变量：

- 每一个节点都有自己的 ID：node_id；
- node_mem_map 就是这个节点的 struct page 数组，用于描述这个节点里面的所有的页；
- node_start_pfn 是这个节点的起始页号；
- node_spanned_pages 是这个节点中包含不连续的物理内存地址的页面数；
- node_present_pages 是真正可用的物理页面的数目。



![伙伴系统](https://i.loli.net/2021/04/03/hcSnjbPOWmAlNvz.jpg)





## 文件系统

### 格式化命令

使用 Windows 的时候，咱们常格式化的格式为**NTFS**（New Technology File System）。在 Linux 下面，常用的是 ext3 或者 ext4。

当一个 Linux 系统插入了一块没有格式化的硬盘的时候，我们可以通过命令**fdisk -l**，查看格式化和没有格式化的分区。



我们可以通过命令**mkfs.ext3**或者**mkfs.ext4**进行格式化。

```
mkfs.ext4 /dev/vdc
```

下面的这个命令行可以启动一个交互式程序

```
fdisk /dev/sda
```

格式化后的硬盘，需要挂在到某个目录下面，才能作为普通的文件系统进行访问。

```
mount /dev/vdc1 / 根目录 / 用户 A 目录 / 目录 1
```

例如，上面这个命令就是将这个文件系统挂在到“/ 根目录 / 用户 A 目录 / 目录 1”这个目录下面。一旦挂在过去，“/ 根目录 / 用户 A 目录 / 目录 1”这个目录下面原来的文件 1 和文件 2 就都看不到了，换成了 vdc1 这个硬盘里面的文件系统的根目录。

有挂载就有卸载，卸载使用**umount**命令。

```
umount / 根目录 / 用户 A 目录 / 目录 1
```



查看文件的类型

```
ls -l
```

![image-20210404142356153](https://i.loli.net/2021/04/04/xhOCEmK4DQlNsLz.png)

相关标识符的解释：

- \- 表示普通文件；
- d 表示文件夹；
- c 表示字符设备文件，这在设备那一节讲解；
- b 表示块设备文件，这也在设备那一节讲解；
- s 表示套接字 socket 文件，这在网络那一节讲解；
- l 表示符号链接，也即软链接，就是通过名字指向另外一个文件，例如下面的代码，instance 这个文件就是指向了 /var/lib/cloud/instances 这个文件。软链接的机制我们这一章会讲解。







### 文件系统功能规划

- **第一点，文件系统要有严格的组织形式，使得文件能够以块为单位进行存储**

- **第二点，文件系统中也要有索引区，用来方便查找一个文件分成的多个块都存放在了什么位置**。

- **第三点，如果文件系统中有的文件是热点文件，近期经常被读取和写入，文件系统应该有缓存层**。

- **第四点，文件应该用文件夹的形式组织起来，方便管理和查询**。

- **第五点，Linux 内核要在自己的内存里面维护一套数据结构，来保存哪些文件被哪些进程打开和使用**。

  > 这就好比，图书馆里会有个图书管理系统，记录哪些书被借阅了，被谁借阅了，借阅了多久，什么时候归还。

![image-20210404141818175](https://i.loli.net/2021/04/04/pq4JNGaP6xcvmHw.png)

要想把很多的文件有序地组织起来，我们就需要把它们成为目录或者文件夹。这样，一个文件夹里可以包含文件夹，也可以包含文件，这样就形成了一种树形结构。而我们可以将不同的用户放在不同的用户目录下，就可以一定程度上避免了命名的冲突问题。

![image-20210404141925166](https://i.loli.net/2021/04/04/kqGr9giJUHjzOBs.png)

![文件系统](https://i.loli.net/2021/04/04/mphCIeZtPg9ELDT.png)





### 硬盘文件系统

![image-20210404144025666](https://i.loli.net/2021/04/04/U218FOYNivZ4zeD.png)

我们常见的硬盘是上面这幅图左边的样子，中间圆的部分是磁盘的盘片，右边的图是抽象出来的图。每一层里分多个磁道，每个磁道分多个扇区，每个扇区是 512 个字节。



我们是不是可以像图书馆那样，也设立一个索引区域，用来维护“某个文件分成几块、每一块在哪里“等等这些**基本信息**?

另外，文件还有**元数据**部分，例如名字、权限等，这就需要一个结构**inode**来存放。

什么是 inode 呢？inode 的“i”是 index 的意思，其实就是“索引”，类似图书馆的索引区域。既然如此，我们每个文件都会对应一个 inode；一个文件夹就是一个文件，也对应一个 inode。



Inode节点信息：

```
struct ext4_inode {
	__le16	i_mode;		/* File mode */
	__le16	i_uid;		/* Low 16 bits of Owner Uid */
	__le32	i_size_lo;	/* Size in bytes */
	__le32	i_atime;	/* Access time */
	__le32	i_ctime;	/* Inode Change time */
	__le32	i_mtime;	/* Modification time */
	__le32	i_dtime;	/* Deletion Time */
	__le16	i_gid;		/* Low 16 bits of Group Id */
	__le16	i_links_count;	/* Links count */
	__le32	i_blocks_lo;	/* Blocks count */
	__le32	i_flags;	/* File flags */
......
	__le32	i_block[EXT4_N_BLOCKS];/* Pointers to blocks */
	__le32	i_generation;	/* File version (for NFS) */
	__le32	i_file_acl_lo;	/* File ACL */
	__le32	i_size_high;
......
};
```

我们刚才说的“某个文件分成几块、每一块在哪里”，这些在 inode 里面，应该保存在 i_block 里面。

具体如何保存的呢？EXT4_N_BLOCKS 有如下的定义，计算下来一共有 15 项。

```
#define	EXT4_NDIR_BLOCKS		12
#define	EXT4_IND_BLOCK			EXT4_NDIR_BLOCKS
#define	EXT4_DIND_BLOCK			(EXT4_IND_BLOCK + 1)
#define	EXT4_TIND_BLOCK			(EXT4_DIND_BLOCK + 1)
#define	EXT4_N_BLOCKS			(EXT4_TIND_BLOCK + 1)
```

在 ext2 和 ext3 中，其中前 12 项直接保存了块的位置，也就是说，我们可以通过 i_block[0-11]，直接得到保存文件内容的块。

![inode节点数据块](https://i.loli.net/2021/04/04/mV4fIADs1Phed6c.jpg)

但是，如果一个文件比较大，12 块放不下。当我们用到 i_block[12] 的时候，就不能直接放数据块的位置了，要不然 i_block 很快就会用完了。这该怎么办呢？我们需要想个办法。我们可以让 i_block[12] 指向一个块，这个块里面不放数据块，而是放数据块的位置，这个块我们称为**间接块**。也就是说，我们在 i_block[12] 里面放间接块的位置，通过 i_block[12] 找到间接块后，间接块里面放数据块的位置，通过间接块可以找到数据块。

如果文件再大一些，i_block[13] 会指向一个块，我们可以用二次间接块。二次间接块里面存放了间接块的位置，间接块里面存放了数据块的位置，数据块里面存放的是真正的数据。如果文件再大一些，i_block[14] 会指向三次间接块。原理和上面都是一样的，就像一层套一层的俄罗斯套娃，一层一层打开，才能拿到最中心的数据块。

如果你稍微有点经验，现在你应该能够意识到，这里面有一个非常显著的问题，对于大文件来讲，我们要多次读取硬盘才能找到相应的块，这样访问速度就会比较慢。

为了解决这个问题，ext4 做了一定的改变。它引入了一个新的概念，叫作**Extents**。

我们来解释一下 Extents。比方说，一个文件大小为 128M，如果使用 4k 大小的块进行存储，需要 32k 个块。如果按照 ext2 或者 ext3 那样散着放，数量太大了。但是 Extents 可以用于存放连续的块，也就是说，我们可以把 128M 放在一个 Extents 里面。这样的话，对大文件的读写性能提高了，文件碎片也减少了。

Exents 如何来存储呢？它其实会保存成一棵树。

![extends](https://i.loli.net/2021/04/04/Dj9oaefyF7sz6KP.jpg)



对于整个文件系统，别忘了咱们讲系统启动的时候说的。如果是一个启动盘，我们需要预留一块区域作为引导区，所以第一个块组的前面要留 1K，用于启动引导区。



![image-20210404150055695](https://i.loli.net/2021/04/04/9IFJldCAXL7mwzx.png)

这里面我还需要重点说一下，超级块和块组描述符表都是全局信息，而且这些数据很重要。如果这些数据丢失了，整个文件系统都打不开了，这比一个文件的一个块损坏更严重。所以，这两部分我们都需要备份，但是采取不同的策略。



### 目录的存储格式

其实目录本身也是个文件，也有 inode。inode 里面也是指向一些块。和普通文件不同的是，普通文件的块里面保存的是文件数据，而目录文件的块里面保存的是目录里面一项一项的文件信息。这些信息我们称为 ext4_dir_entry。从代码来看，有两个版本，在成员来讲几乎没有差别，只不过第二个版本 ext4_dir_entry_2 是将一个 16 位的 name_len，变成了一个 8 位的 name_len 和 8 位的 file_type。



### 软链接和硬链接的存储格式

还有一种特殊的文件格式，硬链接（Hard Link）和软链接（Symbolic Link）。在讲操作文件的命令的时候，我们讲过软链接的概念。所谓的链接（Link），我们可以认为是文件的别名，而链接又可分为两种，硬链接与软链接。通过下面的命令可以创建。



ln -s 创建的是软链接，不带 -s 创建的是硬链接。它们有什么区别呢？在文件系统里面是怎么保存的呢？



![软连接和硬链接](https://i.loli.net/2021/04/04/GCKTPUNtqxpsFmJ.jpg)



 inode 和数据块在文件系统上的关联关系。

![inode 和数据块在文件系统上的关联关系](https://i.loli.net/2021/04/04/BaF3hMRYIikGgeW.png)

你知道如何查看 inode 的内容和文件夹的内容吗？



### 虚拟文件系统

VFS



### 文件缓存





## 进程间通信

### 管道（流程）

管道以文件的形式存在，这也符合 Linux 里面一切皆文件的原则。这个时候，我们 ls 一下，可以看到，这个文件的类型是 p，就是 pipe 的意思。

```
# ls -l
prw-r--r--  1 root root         0 May 21 23:29 hello
```





### 消息队列（邮件）

消息队列有点儿像邮件，发送数据时，会分成一个一个独立的数据单元，也就是消息体，每个消息体都是固定大小的存储块，在字节流上不连续。

这个消息结构的定义我写在下面了。这里面的类型 type 和正文 text 没有强制规定，只要消息的发送方和接收方约定好即可。

```
struct msg_buffer {
    long mtype;
    char mtext[1024];
};
```



### 共享内存（共享大数据内容）

可以看出来，共享会议室这种模型，类似进程间通信的**共享内存模型**。前面咱们讲内存管理的时候，知道每个进程都有自己独立的虚拟内存空间，不同的进程的虚拟内存空间映射到不同的物理内存中去。这个进程访问 A 地址和另一个进程访问 A 地址，其实访问的是不同的物理内存地址，对于数据的增删查改互不影响。

但是，咱们是不是可以变通一下，拿出一块虚拟地址空间来，映射到相同的物理内存中。这样这个进程写入的东西，另外一个进程马上就能看到了，都不需要拷贝来拷贝去，传来传去。

> 需要加锁



### 信号量

信号量其实是一个计数器，主要用于实现进程间的互斥与同步，而不是用于存储进程间通信数据。

对于信号量来讲，会定义两种原子操作，一个是**P 操作**，我们称为**申请资源操作**。这个操作会申请将信号量的数值减去 N，表示这些数量被他申请使用了，其他人不能用了。另一个是**V 操作**，我们称为**归还资源操作**，这个操作会申请将信号量加上 M，表示这些数量已经还给信号量了，其他人可以使用了。



### 信号（紧急情况）

信号没有特别复杂的数据结构，就是用一个代号一样的数字。Linux 提供了几十种信号，分别代表不同的意义。信号之间依靠它们的值来区分。这就像咱们看警匪片，对于紧急的行动，都是说，“1 号作战任务”开始执行，警察就开始行动了。情况紧急，不能啰里啰嗦了。

信号可以在任何时候发送给某一进程，进程需要为这个信号配置信号处理函数。当某个信号发生的时候，就默认执行这个函数就可以了。这就相当于咱们运维一个系统应急手册，当遇到什么情况，做什么事情，都事先准备好，出了事情照着做就可以了。

这种方式有点儿像咱们运维一个线上系统，为了应对一些突发事件，往往需要制定应急预案。就像下面的列表中一样。一旦发生了突发事件，马上能够找到负责人，根据处理步骤进行紧急响应，并且在限定的事件内搞定。

![image-20210406160447428](../../../../AppData/Roaming/Typora/typora-user-images/image-20210406160447428.png)



在 Linux 操作系统中，为了响应各种各样的事件，也是定义了非常多的信号。我们可以通过 kill -l 命令，查看所有的信号。

```
# kill -l
 1) SIGHUP       2) SIGINT       3) SIGQUIT      4) SIGILL       5) SIGTRAP
 6) SIGABRT      7) SIGBUS       8) SIGFPE       9) SIGKILL     10) SIGUSR1
11) SIGSEGV     12) SIGUSR2     13) SIGPIPE     14) SIGALRM     15) SIGTERM
16) SIGSTKFLT   17) SIGCHLD     18) SIGCONT     19) SIGSTOP     20) SIGTSTP
21) SIGTTIN     22) SIGTTOU     23) SIGURG      24) SIGXCPU     25) SIGXFSZ
26) SIGVTALRM   27) SIGPROF     28) SIGWINCH    29) SIGIO       30) SIGPWR
31) SIGSYS      34) SIGRTMIN    35) SIGRTMIN+1  36) SIGRTMIN+2  37) SIGRTMIN+3
38) SIGRTMIN+4  39) SIGRTMIN+5  40) SIGRTMIN+6  41) SIGRTMIN+7  42) SIGRTMIN+8
43) SIGRTMIN+9  44) SIGRTMIN+10 45) SIGRTMIN+11 46) SIGRTMIN+12 47) SIGRTMIN+13
48) SIGRTMIN+14 49) SIGRTMIN+15 50) SIGRTMAX-14 51) SIGRTMAX-13 52) SIGRTMAX-12
53) SIGRTMAX-11 54) SIGRTMAX-10 55) SIGRTMAX-9  56) SIGRTMAX-8  57) SIGRTMAX-7
58) SIGRTMAX-6  59) SIGRTMAX-5  60) SIGRTMAX-4  61) SIGRTMAX-3  62) SIGRTMAX-2
63) SIGRTMAX-1  64) SIGRTMAX
```



- 执行默认操作
- 捕捉信号
- 忽略信号







## 学算法——算法面试通关40讲

《异类-不一样的成功启示录》

- 切碎知识点
- 刻意练习
- 反馈

![image-20210329191135656](https://i.loli.net/2021/03/29/Hz4uZhJsi9yBkvr.png)

![image-20210329191233258](https://i.loli.net/2021/03/29/XKtl67Iv9Tpukqz.png)



### 递归的时间复杂度计算

主定律

