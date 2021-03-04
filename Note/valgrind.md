# Valgrind

## 基本工具

| 工具       | 功能                                                         |
| ---------- | ------------------------------------------------------------ |
| Memcheck   | 内存错误检测器。所有对内存的读写都会被检测到，一切对malloc、free、new、delete的调用都会被捕获。 |
| SGCheck    | SGCheck是一种用于检查栈中和全局数组溢出的工具。              |
| Cachegrind | 缓存和分支预测探查器。它可以帮助您使程序运行更快。           |
| Callgrind  | 生成调用图的缓存分析器。它与Cachegrind有一些重叠，但也收集了Cachegrind没有的一些信息。 |
| Helgrind   | 线程错误检测器。它可以帮助您使多线程程序更正确。             |
| DRD        | 线程错误检测器。它类似于Helgrind，但使用不同的分析技术，因此可能会发现不同的问题。 |
| Massif     | 堆分析器。它可以帮助您减少程序使用的内存。                   |
| DHAT       | 堆分析器。它可以帮助您了解模块寿命，模块利用率和布局效率低下的问题。 |
| BBV        | 实验性SimPoint基本块矢量生成器                               |

还有一些次要工具对大多数用户没有用： **Lackey**是一个示例工具，它说明了一些仪器基础知识；和**Nulgrind**是最小Valgrind的工具，做任何分析或仪器，且仅用于测试目的。

(Valgrind与CPU和操作系统的细节紧密相关，而在较小程度上与编译器和基本C库紧密相关.)

### Memcheck

[Memcheck](https://blog.csdn.net/u010168781/article/details/83749609)是最常用的工具，用来检测程序中出现的内存问题，所有对内存的读写都会被检测到，一切对malloc、free、new、delete的调用都会被捕获。所以，它能检测以下问题：

1、对未初始化内存的使用；

2、读/写释放后的内存块；

3、读/写超出malloc分配的内存块；

4、读/写不适当的栈中内存块；

5、内存泄漏，指向一块内存的指针永远丢失；

6、不正确的malloc/free或new/delete匹配；

7、memcpy()相关函数中的dst和src指针重叠。

这些问题往往是C/C++程序员最头疼的问题，Memcheck能在这里帮上大忙。

检测命令：

```
valgrind --tool=memcheck ./a.out
```

或者(默认工具是memcheck)

```
valgrind --leak-check=yes ./a.out
```



### SGCheck

[SGCheck](https://blog.csdn.net/u010168781/article/details/83784492)是一种用于检查栈中和全局数组溢出的工具。它的工作原理是使用一种启发式方法，该方法源于对可能的堆栈形式和全局数组访问的观察。

1. 栈中的数据：例如函数内声明数组int a[10]，而不是malloc分配的，malloc分配的内存是在堆中。
2. SGCheck和Memcheck是互补的：它们的功能不重叠。
3. Memcheck对堆数组（如malloc分配的内存）执行边界检查和使用后检查。它还可以检查堆或栈分配创建的未初始化值（值的有效性检查）。但它不会对栈或全局数组执行边界检查。
4. SGCheck只对栈或全局数组进行边界检查，不做其它检查。

SGCheck没有命令行参数，使用方法如下：

```
valgrind --tool=exp-sgcheck ./a.out
```

如果使用Memcheck无法检查处栈中数组越界的错误，可以使用这个工具。

限制：

1. 遗漏错误，第一次使用栈或全局数组时就溢出了，该情况无法检查。
2. 误报
3. SGCheck的运行速度比Memcheck慢。
4. 栈或全局数组检查在PowerPC、ARM或S390X平台上无法正常工作，仅适用于X86和AMD64平台。

### Callgrind

[Callgrind](https://blog.csdn.net/u010168781/article/details/84303954)收集程序运行时的一些数据，建立函数调用关系图，还可以有选择地进行cache模拟。在运行结束时，它会把分析数据写入一个文件。callgrind_annotate可以把这个文件的内容转化成可读的形式。

在程序终止时将配置文件数据写出到文件。为了呈现数据和交互式控制分析，提供了两个命令行工具：

```
callgrind_annotate
```

此命令读入配置文件数据，并打印已排序的函数列表，可选择使用源注释。
对于数据的图形可视化可以使用 KCachegrind。

```
callgrind_control
```

使用此命令可以交互式地观察和控制当前在Callgrind控件下运行的程序的状态，而无需停止程序。

上一篇介绍过Cachegrind，Cachegrind用于收集：事件计数（数据读取，缓存未命中等）。而Callgrind用于记录函数成本。例如：函数foo调用 bar，则将成本bar加入到 foo成本中。使用callgrind_annotate或KCachegrind可以查看从main开始的调用关系图，可以查看各个点的成本，便于优化代码。

Callgrind检测函数调用和返回的能力取决于它运行的平台的指令集。它最适用于x86和amd64，遗憾的是目前在PowerPC，ARM，Thumb或MIPS代码上运行效果不佳。这是因为这些指令集中没有显式的调用或返回指令，因此Callgrind必须依靠启发式方法来检测调用和返回。



分析命令：

```
valgrind --tool=callgrind  ./a.out
```

执行完上述命令后，在当前目录下生成 callgrind.out.<pid>文件，使用可视化kcachegrind查看

```
kcachegrind callgrind.out.21479
```



### Cachegrind

[Cachegrind](https://blog.csdn.net/u010168781/article/details/84137730)是Cache分析器，它模拟CPU中的一级缓存I1，Dl和二级缓存，能够精确地指出程序中cache的丢失和命中。如果需要，它还能够为我们提供cache丢失次数，内存引用次数，以及每行代码，每个函数，每个模块，整个程序产生的指令数。这对优化程序有很大的帮助。

Cachegrind收集以下统计信息（括号中给出了每个统计信息使用的缩写）：
I 缓存读取（Ir，它等于执行的指令数），I1缓存读取未命中（I1mr）和LL缓存指令读取未命中（ILmr）。
D缓存读取（Dr等于内存读取的数量），D1缓存读取未命中（D1mr）和LL缓存数据读取未命中（DLmr）。
D缓存写入（Dw等于内存写入次数），D1缓存写入未命中（D1mw）和LL缓存数据写入未命中（DLmw）。
条件分支执行（Bc）和条件分支错误预测（Bcm）。
间接分支执行（Bi）和间接分支错误预测（Bim）。
注意，D1总访问量由D1mr+ 给出 D1mw，LL总访问量由ILmr+ DLmr+ 给出DLmw。

在现代CPU上，L1未命中通常将花费大约10个周期，LL未命中可能花费多达200个周期，并且分支错误预测的成本在10到30个周期。详细的缓存和分支分析对于了解程序如何与CPU进行交互以及如何使其更快地运行非常有用。

检测命令：

```
valgrind --tool=cachegrind  ./a.out
```

### Helgrind

它主要用来检查多线程程序中出现的竞争问题。Helgrind寻找内存中被多个线程访问，而又没有一贯加锁的区域，这些区域往往是线程之间失去同步的地方，而且会导致难以发掘的错误。Helgrind实现了名为“Eraser”的竞争检测算法，并做了进一步改进，减少了报告错误的次数。不过，Helgrind仍然处于实验阶段。

### DRD

[DRD](https://blog.csdn.net/u010168781/article/details/84032369)用于线程错误检测例如：数据竞争；锁竞争；POSIX线程API使用不当；死锁；

编译

```
gcc -g main.c -pthread
```

使用检测

```
valgrind --tool=drd ./a.out
```

### Massif

[Massif](https://blog.csdn.net/u010168781/article/details/83788559)是堆栈分析器，它能测量程序在堆栈中使用了多少内存，告诉我们堆块，堆管理块和栈的大小。Massif能帮助我们减少内存的使用，在带有虚拟内存的现代系统中，它还能够加速我们程序的运行，减少程序停留在交换区中的几率。

Massif对内存的分配和释放做profile。程序开发者通过它可以深入了解程序的内存使用行为，从而对内存使用进行优化。这个功能对C++尤其有用，因为C++有很多隐藏的内存分配和释放。

使用：

1. gcc编译源码时，添加 -g 选项，Massif对编译优化没有要求。

2. 检测命令：

   ```
   valgrind --tool=massif --time-unit=B ./a.out
   ```

3. 使用 ms_print massif.out.PID查看分析结果



### DHAT

[DHAT](https://blog.csdn.net/u010168781/article/details/83861592)是动态堆分析器。Massif（堆分析器）是在程序结束后输出分析结果，而DHAT是实时输出结果，所以叫做动态堆分析器。Massif只记录堆内存的申请和释放，DHAT还会分析堆空间的使用率、使用周期等信息。
DHAT的功能：它首先记录在堆上分配的块，通过分析每次内存访问时所指定的块判断是否是之前已经记录过的块，并收集统计这些信息，最终输入如下结果：

1. 总共分配的堆内存数（字节数和块数）；
2. 程序运行中堆内存的最大数（字节数和块数）；
3. 块平均寿命（从分配到释放之间的指令数）；
4. 块中每个字节的平均读写次数（“访问率”）；
5. 对于总是仅分配一个大小的块的分配点，该大小为4096字节或更少：计数表示访问块内每个字节偏移的频率。

使用这些统计信息可以得出以下结果：

1. 潜在的泄漏（进程生命周期内）：由该点分配的块只是累积，并且仅在运行结束时释放；
2. 过度浪费内存（英文原文excessive turnover）：由该点分配的块只是累积，吞噬很多堆内存，但会释放，不会保持很长时间；
3. 过度瞬态内存：从分配到释放的时间非常短；
4. 无用或未充分利用的内存：已分配但未完全使用的内存，或只写到内存中但随后并没有读的内存；
5. 使用效率低的块

检测命令：

```
valgrind --tool=exp-dhat ./a.out
```

结果分析：

1. 概要信息

   guest_insns：程序运行的指令总数；
   max_live：程序运行期间堆内存最大值；
   tot_alloc：程序运行期间堆内存累计值；
   insns per allocated byte：平均每到每条指令分配的堆内存数

2. 每次分配的堆内存的信息

   max-live：程序运行期间堆内存最大值
   tot-alloc：程序运行期间堆内存累计值
   deaths：释放次数
   acc-ratios：分配的块中的每个字节平均读写次数

## 基本操作

### 使用

使用的一般格式：

```
valgrind [valgrind-options] your-prog [your-prog-options]
```

运行valgrind -h可以查看详细使用方法，命令格式如下：

```
valgrind [valgrind -h中的选项] 待测程序 [待测程序的命令行参数列表]
```

最重要的选项是–tool决定运行哪种Valgrind工具。

`--tool=<toolname> [default: memcheck]`：最常用的选项。运行valgrind中名为*toolname*的工具。如果省略工具名，默认运行memcheck。

例如，使用内存检查工具Memcheck 运行“ls -l”命令 ，执行命令格式如下：

```
valgrind --tool = memcheck ls -l
```

Memcheck是默认设置，因此如果要使用它，则可以省略该–tool选项，如：

```
valgrind  ls -l
```

`--db-attach=<yes|no> [default: no]`：绑定到调试器上，便于调试错误。

### 原理

无论使用哪种工具，Valgrind都会在程序启动前控制待测程序。从可执行文件和相关库中读取调试信息，以便在适当时可以根据源代码位置来表示错误消息和其他输出。

然后，待测程序将在Valgrind核心提供的“合成CPU”上运行。当新代码首次执行时，Valgrind核心将程序代码交给选定的工具。该工具将自己的检测代码添加到此处，并将结果交还给核心，核心协调持续执行此检测代码。

添加的检测代码量在不同工具之间差异很大。Memcheck添加了代码来检查每个内存访问和计算的每个值，使其运行速度比本机慢10-50倍。为Nulgrind（最小工具）根本不添加任何仪器，运行速度比本机慢4倍。

Valgrind模拟程序执行的每条指令。因此，活动工具不仅检查应用程序中的代码，还检查所有支持动态链接库（包括C库，图形库等）的代码。

如果使用的是错误检测工具，Valgrind可能会检测系统库中的错误，例如使用了GNU C或X11库。尽管对这些错误不感兴趣，但是无法控制该代码。因此，Valgrind允许通过将它们记录在Valgrind启动时读取的“抑制文件”（后续会专门讲）中来有选择地抑制错误（就是系统库中的错误不打印出来）。Valgrind构建机制选择默认抑制，为计算机上检测到的操作系统和库提供合理的行为。为了更容易编写抑制，可以使用该 --gen-suppressions=yes选项。这告诉Valgrind打印出每个报告错误的抑制，然后可以将其复制到抑制文件中。

不同的错误检查工具会报告不同类型的错误。因此，抑制机制允许标记出每个抑制适用于哪个工具或工具。

### 编译时说明

1. 最好使用debug版本（gcc -g），这样打印的信息中会将错误和分析的信息指定出相关的代码行；
2. 如果是C++最好将内联函数以普通函数对待（gcc -fno-inline），这样更容易看到函数调用链，这有助于减少在大型C ++应用程序中导航时的混淆；
3. 不要使用优化（gcc -O2或gcc-O1等），这会导致Memcheck错误地报告未初始化的值错误或丢失未初始化的值错误；
4. 最好编译时能显示所有警告（gcc -Wall）





## 使用核心

### 输出评估

输出格式如下：

```
==12345== some-message-from-Valgrind
```

12345是进程ID

默认valgrind输出重要的信息，如果需要输出更多的信息，添加-v选项。

可以将评论指向几个地方：

1. 默认发送到文件描述符2。也可以指定其他描述符例如9： `--log-fd=9`

2. 一个侵入性较低的选项是将这个评估写到一个文件中，例如： `--log-file=filename`

3. 侵入性最小的选择是将评估发送到网络socket。例如：`--log-socket=192.168.0.1:12345`。

   (You can also omit the port number: `--log-socket=192.168.0.1`, in which case a default port of 1500 is used. This default is defined by the constant `VG_CLO_DEFAULT_LOGPORT` in the sources.)(只能用IP地址，不能是主机名)



### 错误报告

当错误检查工具检测到程序中发生了错误时，就会向注释中写入一条错误消息。以下是来自Memcheck的一个例子：

```
==25832== Invalid read of size 4
==25832==    at 0x8048724: BandMatrix::ReSize(int, int, int) (bogon.cpp:45)
==25832==    by 0x80487AF: main (bogon.cpp:66)
==25832==  Address 0xBFFFF74C is not stack'd, malloc'd or free'd
```

这个消息说，程序非法读取了地址0xBFFFF74C的4字节，据Memcheck所知，这不是一个有效的堆栈地址，也不对应于任何当前堆块或最近释放的堆块。发生在bogon.cpp的第45行。从同一文件的第66行调用，等等。对于与已识别的(当前或已释放的)堆块相关的错误，例如读取已释放的内存，Valgrind不仅会报告错误发生的位置，还会报告分配/释放关联堆块的位置。

![image-20201218094159386](https://i.loli.net/2020/12/18/BOnFNMJ59QEIKju.png)



### 抑制错误

错误检查工具可以检测操作系统中预装的系统库中的许多问题，例如C库。您无法轻松地解决这些问题，但是您不想看到这些错误（是的，有很多！），因此Valgrind读取了要在启动时消除的错误列表。`./configure`构建系统时，脚本会创建一个默认的禁止文件 。

您可以随意修改并添加到抑制文件，或者更好地编写自己的文件。允许多个抑制文件。如果项目的一部分包含您无法或不想修复的错误，但又不想不断地被提醒，则此功能非常有用。

**注意：** 到目前为止，添加抑制的最简单方法是使用“[核心命令行选项”中](https://valgrind.org/docs/manual/manual-core.html#manual-core.options)`--gen-suppressions=yes`描述的[选项](https://valgrind.org/docs/manual/manual-core.html#manual-core.options)。这将自动生成抑制。为了获得最佳结果，您可能需要`--gen-suppressions=yes`手动编辑输出





### Core Command-line Options

The single most important option.

- `--tool=<toolname> [default: memcheck]`

  运行名为toolname的Valgrind工具，如memcheck, cachegrind, callgrind, helgrind, drd, massif, dhat, lackey, none, expi -bbv等

#### Basic Options

These options work with all tools.

- `-h --help`：显示帮助
- `--help-debug`：显示帮助以及debug操作
- `--version`：显示版本
- `-q`, `--quiet`：静默运行，只显示错误信息
- `-v`, `--verbose`：显示更多的信息
- `--trace-children=<yes|no> [default: no]`：启用后，Valgrind将跟踪到通过exec系统调用启动的子进程。这对于多进程程序是必需的。
- `--trace-children-skip=patt1,patt2,...`：当 `--trace-children=yes`时有效，允许一些进程跳过检查，支持正则表达式。
- `--trace-children-skip-by-arg=patt1,patt2,...`：和`--trace-children-skip`类似，唯一的区别是：决定是否跟踪到子进程是通过检查子进程的参数而不是其可执行文件的名称来决定的。
- `--child-silent-after-fork=<yes|no> [default: no]`：当启用时，Valgrind将不会显示由fork调用产生的子进程的任何调试或日志输出
- `--vgdb=<no|yes|full> [default: yes]`：当指定`--vgdb=yes`或`--vgdb=full`时，Valgrind将提供“gdbserver”功能。这允许一个外部的GNU GDB调试器在您的程序运行在Valgrind上时控制和调试程序。——vgdb=full带来了显著的性能开销，但提供了更精确的断点和观察点
- `--vgdb-error=<number> [default: 999999999]：当使用`--vgdb=yes`或`--vgdb=full`启用Valgrind gdbserver时，请使用此选项。报告错误的工具将等待“number”错误报告，然后冻结程序并等待您与GDB连接。因此，0的值将导致gdbserver在执行程序之前启动。这通常用于在执行之前插入GDB断点，也适用于不报告错误的工具，比如Massif。
- `--vgdb-stop-at=<set> [default: none]`：当使用`--vgdb=yes`或`--vgdb=full`时，在`--vgdb-error`被报告后，Valgrind gdbserver将为每个错误调用。你可以另外请求Valgrind gdbserver为其他事件调用，用以下方法之一指定
  * 一个或多个启动出口valgrindabexit的逗号分隔列表。
  * `all` to specify the complete set. It is equivalent to `--vgdb-stop-at=startup,exit,valgrindabexit`.
  * `none` for the empty set.
- `--track-fds=<yes|no> [default: no]`：启用后，Valgrind将在退出或请求时通过gdbserver monitor命令v.info open_fds打印打开的文件描述符列表。
- `--time-stamp=<yes|no> [default: no]`：启用后，每条消息的前面都有启动后经过的wallclock时间的指示，表示为天、小时、分钟、秒和毫秒。



#### 错误相关的选项(部分)

所有可以报告错误的工具（例如Memcheck，但不是Cachegrind）都使用这些选项

* `--xml=<yes|no> [default: no]`：启用后，输出的重要部分（例如，工具错误消息）将采用XML格式，而不是纯文本。此外，XML输出将被发送到与纯文本输出不同的输出通道。因此，还必须使用的一个 `--xml-fd`，`--xml-file`或 `--xml-socket`到指定的XML将被发送。

* `--xml-fd=<number> [default: -1, disabled]`指定Valgrind应该将其XML输出发送到指定的文件描述符。它必须与结合使用 `--xml=yes`。

* `--xml-file=<filename>`：指定Valgrind应该将其XML输出发送到指定的文件。它必须与结合使用 `--xml=yes`。文件名中出现的任何`%p`或 `%q`序列都以与完全相同的方式扩展`--log-file`。。

* `--xml-socket=<ip-address:port-number>`指定Valgrind应该在指定的IP地址将其XML输出发送到指定的端口。它必须与结合使用`--xml=yes`。参数的形式与所使用的形式相同`--log-socket`。

* `--xml-user-comment=<string>`：在XML输出的开头嵌入一个额外的用户注释字符串。仅在`--xml=yes`指定时有效；否则忽略。

* `--demangle=<yes|no> [default: yes]`：启用/禁用C ++名称的自动拆分（解码）。默认启用。启用后，Valgrind将尝试将编码的C ++名称转换回接近原始名称的名称。分解器处理由g ++版本2.X，3.X和4.X破坏的符号。

* `--num-callers=<number> [default: 12]`：指定用于标识程序位置的堆栈跟踪中显示的最大条目数。请注意，错误仅使用前四个函数位置（当前函数中的位置以及它的三个直接调用方的位置）共同出现。因此，这不会影响报告的错误总数。最大值为500。请注意，较高的设置将使Valgrind运行更慢，并且占用更多的内存，但是在使用具有深层嵌套调用链的程序时可能很有用。

* `--unw-stack-scan-thresh=<number> [default: 0] `， `--unw-stack-scan-frames=<number> [default: 5]`：堆栈扫描支持仅在ARM目标上可用。这些标志通过堆栈扫描启用和控制堆栈展开。当正常的堆栈退卷机制（使用Dwarf CFI记录和跟随帧指针）失败时，堆栈扫描可能能够恢复堆栈跟踪。

* `--error-limit=<yes|no> [default: yes]`：启用后，Valgrind将在总共看到10,000,000个或1,000个不同的错误后停止报告错误。这是为了防止错误跟踪机制在具有许多错误的程序中成为巨大的性能开销。

* `--error-exitcode=<number> [default: 0]`：指定一个替代退出代码，如果Valgrind报告运行中有任何错误，则返回该退出代码。当设置为默认值（零）时，Valgrind的返回值将始终是被模拟过程的返回值。设置为非零值时，如果Valgrind检测到任何错误，则返回该值。这对于将Valgrind用作自动化测试套件的一部分很有用，因为通过检查返回码，可以很容易地检测Valgrind报告了错误的测试用例。
* `--exit-on-first-error=<yes|no> [default: no]`：如果启用此选项，Valgrind将在第一个错误时退出。必须使用`--error-exitcode`option定义非零退出值 。如果您正在运行回归测试或具有其他一些自动化测试机制，则很有用。

* `--error-markers=<begin>,<end> [default: none]`：当错误以纯文本格式输出（即未使用XML）时， `--error-markers`指示在每个错误之前（之后）输出包含`begin`（`end`）字符串的行。这样的标记行有助于在包含与程序输出混合的valgrind错误的输出文件中搜索错误和/或提取错误。
* `--show-error-list=no|yes [default: no]`：如果启用此选项，则对于报告错误的工具，valgrind将在退出时显示检测到的错误列表和已使用的抑制列表。请注意，在详细程度2和更高的级别，除非`--show-error-list=no`选中，否则valgrind会自动显示检测到的错误列表和退出时使用的抑制列表 。

* `-s`：指定`-s`等同于 `--show-error-list=yes`。

* `--sigill-diagnostics=<yes|no> [default: yes]`启用/禁用非法指令诊断的打印。默认情况下启用，但`--quiet`给定时默认为禁用 。通过提供此选项，可以始终明确覆盖默认值。启用后，在程序收到SIGILL信号之前，每当遇到Valgrind无法解码或翻译的指令时，将打印警告消息以及一些诊断信息。通常，非法指令表示程序中存在错误或对Valgrind中的特定指令缺少支持。但是某些程序确实故意执行一条可能丢失的指令，并捕获SIGILL信号以检测处理器功能。使用此标志可以避免在这种情况下可能获得的诊断输出。。

* `--max-stackframe=<number> [default: 2000000]` ：堆叠框架的最大大小。如果堆栈指针的移动量超过此数量，则Valgrind将假定程序正在切换到其他堆栈。

* `--main-stacksize=<number> [default: use current 'ulimit' value]`：指定主线程堆栈的大小。

* `--max-threads=<number> [default: 500]`：默认情况下，Valgrind最多可以处理500个线程。有时，该数字太小。使用此选项可以提供不同的限制。例如 `--max-threads=3000`。



#### 与malloc相关的选项

对于使用自己版本的工具 `malloc`（例如Memcheck，Massif，Helgrind，DRD），以下选项适用。

- `--alignment=<number> [default: 8 or 16, depending on the platform]`

  默认情况下，Valgrind的`malloc`， `realloc`等返回一个起始地址为8字节对齐或16字节对齐的块（该值取决于平台并与平台默认值匹配）。此选项使您可以指定其他对齐方式。提供的值必须大于或等于默认值，小于或等于4096，并且必须是2的幂。

- `--redzone-size=<number> [default: depends on the tool]`

  Valgrind`malloc, realloc,`等在运行程序分配的每个堆块之前和之后添加填充块。这种填充块称为redzone。Redzone大小的默认值取决于工具。例如，Memcheck在客户端分配的每个块之前和之后添加并保护至少16个字节。这样，它就可以检测最多16个字节的块下溢或溢出。增加红色区域的大小可以检测较大距离的超限，但可以增加Valgrind使用的内存量。减小Redzone大小将减少Valgrind所需的内存，但也会减少检测到超限/不足的机会，因此不建议这样做。

- `--xtree-memory=none|allocs|full [none]`

  替换Valgrind`malloc, realloc,`等的工具可以有选择地生成一个执行树，详细说明哪段代码负责堆内存的使用。有

- `--xtree-memory-file=<filename> [default: xtmemory.kcg.%p]`

  指定Valgrind应在指定文件中生成xtree内存报告。文件名中出现的任何`%p`或 `%q`序列都以与完全相同的方式扩展`--log-file`。