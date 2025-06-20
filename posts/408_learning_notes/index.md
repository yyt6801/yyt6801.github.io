# 408学习笔记


--------------------------------------------
# BUPT---  803计算机学科基础综合
## 试卷内容结构
　　数据结构 45分
　　计算机组成原理 45分
　　操作系统 35分
　　计算机网络 25分
## 试卷题型结构
　　单项选择题 80分 (40小题，每小题2分)
        综合应用题 70分
        
#### 选择40道(80&#39;)
##### 分值构成:
1~11 数据结构22&#39; (11*2)
12~22 计算机组成原理22&#39; (11*2)
23~33 操作系统20&#39; (10*2)
33~40 计算机网络16&#39; (8*2)
#### 大题共有7道题:(70&#39;)
##### 分值构成:
2018: 10&#43;13&#43;11&#43;12&#43;7&#43;8&#43;9
2017: 10&#43;13&#43;11&#43;12&#43;7&#43;8&#43;9
2016: 8&#43;15&#43;11&#43;12&#43;7&#43;8&#43;9
41,42---数据结构23&#39;
43,44---计算机组成原理23&#39;
45,46---操作系统15&#39;
47---计算机网络9&#39;

### 大题前两道-数据结构题: 
##### 2018(10&#39;) 1.队列顺序存储-假溢出-解决方法,队列当前长度,判定队空/队满
##### 2018(13&#39;) 2.设计算法---哈系表存储-哈系函数-链地址法处理冲突-设计哈系表的初始化,插入元素和删除元素的算法

##### 2017(10&#39;) 有向图::有向图描述，两定点间的权值，画有向图，迪杰斯特拉算法求顶点到顶点的最短距离
##### 2017(13&#39;) 设计算法---Search_Insert非空二叉排序树-查找元素值为e的结点,若存在则返回指针,若不存在则插入一个元素值为e的新结点,并返回新结点的指针

##### 2016(8&#39;) 二叉树-先序-中序-后序;画二叉树
##### 2016(15&#39;) 设计算法---邻接矩阵-顶点到顶点有边：求矩阵的传递包：使得若从顶点到顶点有一条或多条路径

### 大题43,44-计算机组成原理题: 
##### 2018(11&#39;) 主存地址空间-字节编址-指令Cache和数据Cache分离,数据Cache采用直接映射..........补码,行优先存放/列优先存放,主存块对应的Cache行号


--------------------------------------------------------------


# 2016年803学科综合计算机(个人整理历年考点汇总)
## 知识点：
数据的存储结构术语： 循环队列？线索树？栈？数组？
栈-栈满条件
带头节点-单循环链表-非空队列，队列指针，新元素结点，
模式串-nextval数组值
关键字的3阶B树，关键字的结点个数
关键路径-AOE网络， 源点-汇点，最长回路-最短回路
强连通图-边数
顺序存储结构排序算法-关键字比较次数-元素初始排列次序，快速排序、堆排序、直接插入排序、简单选择排序
堆排序-初始堆
二叉排序树-平均查找长度-数量级，顺序查找、折半查找、分块查找
冯·诺依曼计算机-特点： 二进制？存储程序？控制流驱动方式？数据流驱动方式？
8位计算机存储器按字节编码-存储器单元的值，补码？负数？值？
IEEE754浮点数格式，？尾数-阶码-补码-规格化数-非规格化数？
八体低位交叉存储器，容量-存储周期-最大带宽
八路组相联Cache块-主存块-字节-标记、组号、块内地址
累加器-堆栈指示器-栈顶单元，进栈操作顺序-出栈操作顺序
四级流水线-浮点加法器-流水线时钟周期-不是流水线方式所需时间
PCI总线特性， 
进程从运行状态转为就绪状态的条件
进程-线程
单处理器并发执行进程-最小平均等待时间
分页内存管理系统-物理内存空间大小-逻辑地址空间-页面大小-页表进行逻辑地址到物理地址，定义页框（帧）号的位数
I/O设备中引入缓冲机制的目的
死锁问题处理机制-银行家算法
位图管理磁盘自由空间-磁盘块-位图占用字节数
CPU调度算法-硬实时系统
硬盘调度算法-先来先服务磁盘调度算法（FCFS）
UDP-七层参考模型
带宽-信道-二进制信号-信噪比-最大传输速率
信道噪音-数据链路层成帧方法
链路状态路由选择协议
IP-子网掩码-广播数据-IP数据报目的地址
IPv6地址长度
TCP协议-慢启动算法
DNS
## 核心知识点
1、二叉树-先序-中序-后序，画二叉树
2、邻接矩阵-顶点--算法：求矩阵传递包：从顶点到顶点有一条或多条路径
3、字长-主存地址空间大小-字节编址-双字长指令；操作码-寄存器，寻址方式；操作数地址-目的操作数地址的位移量或立即数-补码；。。。指令系统最多定义多少条指令；。。。指令的机器码格式；。。。执行指令SAR R7,2后内容；十六进制-十进制
4、处理器主频-时钟周期-鼠标输入输入的开销(处理器用于鼠标输入的时间占整个处理器时间的百分比)；。。。数据传输速率，查询一次课传输多少字节-查询软盘的开销
5、请求页式内存管理系统-进程页面引用串；分配给该进程的可用页框（帧）数目，采用FIFO页面替换算法。计算进程页面访问过程中发生的缺页次数和缺页率
6、多进程共享有限缓冲区，缓冲区容量，最多容纳N个数据项，向换成冲去写入数据项，缓冲区提取数据项。定义信号量并用wait、signal(P、V操作)实现生产者、消费者进程对缓冲区的并发访问。
7、数据链路层采用滑动窗口机制，用64kbps的信道传输长度为1024比特的数据帧，信道的单向传播延迟为256ms，应答帧和数据帧枕头的开销忽略不计。。。计算使用停等协议时信道利用率。计算使用发送窗口为7时GO-BACK-N协议的信道利用率。。。为使信道利用率最大，使用GO-BACK-N协议时帧头中序号字段至少为多少比特。。。为避免无谓的重传，滑动窗口协议的超时重发计时器至少为多少？


# 2017_803_计算机学科基础综合
## 知识点
算法的时间复杂度
单链表存储-归并-比较次数
单循环链表存储-队列插入和删除操作时间复杂度
数组的元素占用存储单元数
结点-树-高度最小和最大
树的存储形式
强连通图-边数最多
图-
折半查找与顺序查找次数
排序算法-希尔排序-时间复杂度-快速排序，效率
堆排序方法-初始堆
冯·诺依曼
定点整数计算机-通用寄存器位数-（R0）-位数-寄存器R0的真值
IEEE754单精度浮点数十六进制值-十进制
储存器-动态储存器-FLASH-ROM-DRAM
四体低位交叉存储器-存取周期-每个单体的存储容量为IMx32位，存储器容量
变址寻址-有效地址-主存，指令的寻址和数据寻址，堆栈寻址
RISC指令系统特征
机器指令-微指令
显存容量-分辨率-像素最多可使用的颜色数
总线-一个总线周期-并行传送多少字节数据-总线时钟频率-每个总线周期等于一个总线时钟周期，总线带宽？
单级中断系统-CPU响应中断-
系统引导过程-CPU首先执行的代码时？
进程-进程控制块将会首先插入到的队列是？-就绪队列-等待队列-运行队列-活动队列
n个用户进程-等待队列中用户进程个数最多？
在多进程系统中-各进程应互斥进入临界区-临界区指？
交互式系统-用户数为10-为保证响应时间&lt;=100ms，操作系统应将时间片设为？
进程调度算法的选择-使系统有最高的吞吐量
使用文件必须先做的操作
一般在文件系统中采用树型目录
一级索引结构的文件系统-磁盘块大小-文件大小-需要占用的磁盘块
磁带上的文件只能是？-顺序存取-随机存取-以双字为单位存取-直接存取
信道带宽-信噪比-信道最大数据传输速率
回退N步协议-帧头中序号字段-发送窗口的最大值
数据链路层采用CRC校验-生成多项式-待发送比特流-校验信息
生成转发表-接收帧中地址？-目的MAC地址-源MAC地址-目的IP地址-源IP地址
IP数据报头中源IP地址
协议-IP-TCP-DNS-OSPF
IP数据报头中设置TTL字段目的
TCP协议发送数据，MSS字节，拥塞窗口和接收窗口字节，定时器超时时的发送窗口大小
## 核心知识点
1、向图描述，两定点间的权值，画向图，迪杰斯特拉算法求顶点到顶点的最短距离
2、算法，非空二叉排序树，结点，指针
3、主存，编址，容量、Cache容量，每块、每字，指令Load/Store,存储器读写操作，寄存器操作，指令种类/指令所占比列，Cache命中率，Cache访问命中时的CPI，访问不命中损失时钟周期：  CPI，Cache四路组相联映射/直接映射时主存地址中各个字段位数；Cache命中率-Cache缺失相比下的速度
4、数据通路，通用寄存器，内存数据缓冲寄存器，内存地址寄存器，程序计数器，指令寄存器，内存，控制信号。二进制RS型指令格式，减法SUB指令执行周期各节拍的功能和控制信号。 设计模型机的操作控制器，设计方法，优缺点
5、实现同步互斥算法实现过程
6、访问串，分配给进程的内存空间为4个空闲块物理块，LRU算法，计算访问过程中发生的缺页次数和缺页率。
7、TCP/IP协议通讯，路由器，通信链路的MTU为多大字节，从主机发送长度为1400字节的UDP数据包到主机B，封装UDP数据包的IP数据报沿途需经过分片，分片和重装分别发生在哪些设备上？。。。IP报头字段中，哪些字段与分片和重装相关。。。使用HTTP协议从主机B下载800M字节文件，TCP如何探知主机A到主机B的“路径MTU”以避免IP层的分片。。。。简述UDP,TCP,IP,ARP协议提供的服务


2021年专业课改为408计算机综合
以上大纲及考点侧重点有较大偏差，从今起重新开始分析408考研大纲
数据结构&#43;计算机组成原理&#43;操作系统&#43;计算机网络

计算机组成原理：
我自己看书是没什么感觉的，我需要跟着视频去看，有自己的思维方式，把自己会的知识串起来，可以倍速看，看完后需要做题，一定一定要做题，做题中去查漏补缺。视频可以反复看
4本书，视频过一遍，估计会很慢，所以一定要做题，无论对错，搞懂是最基本的
知识框架要做到心中有数，自己能把整个知识体系框架建立起来，深入理解大纲要求，自己能够把整个大纲知识体系整理出来
做真题，反复做。从真题中才能学到东西，最贴近考点   不用留着，直接开始  拿到哪套做哪套


# 计算机基础知识概要
###### (以下大部分内容选自871计算机综合考试大纲)
## 计算机组成原理

### 1.计算机系统概述
#### 1）电子计算机与存储程序控制。
    计算机发展历史：
    数字化概念：
    存储程序工作方式：
    冯诺依曼体制：
#### 2）计算机系统层次结构：
    计算机硬件的基本组成：
    计算机软件的分类：
    计算机的工作过程：
##### （1）计算机系统：
        计算机硬件系统的组织：
        硬件与软件间的关系：
        计算机系统软硬件的逻辑等效性：
##### （2）计算机系统层次结构概念：
        系列机和软件兼容：
#### 3）计算机性能指标：
        吞吐量：
        响应时间：
        CPU时钟周期：
        主频：
        CPI:
        CPU执行时间：
        MIPS:
        MFLOPS:

### 2.数据的机器层次表示：
#### 1）数值数据的表示。
        进位计数制基本概念：
        原码、补码表示方法及相互转换：
#### 2）机器数的定点表示和浮点表示：
        定点整数、定点小数表示：
        浮点数表示方法和表示范围：
        规格化和隐藏位等技术：
        阶码的移码表示法及IEEE754标准：
        定点、浮点表示法的区别：
        定点、浮点计算机：
#### 3）非数值数据的表示：
        字符和字符串的表示：
        汉字的编码及统一代码（Unicode）
#### 4）十进制数和数串的表示：
        常见的十进制数的编码方法以及十进制数串的存储方法：
        现代微型计算机系统中各种数据的表示方法：
#### 5）数据校验码：
        数据校验码的概念和实现原理：
        奇偶校验：
        海明校验的原理与实现方法：

### 3.指令系统：
#### 1）指令格式：
        扩展操作码指令集设计的基本方法：
        指令的基本格式：
        定长操作码指令格式：
        扩展操作码指令格式：
#### 2）指令的寻址方式：
        有效地址的概念：
        数据寻址和指令寻址：
        常见寻址方式：
        堆栈的结构和堆栈操作：
#### 3）CISC和RISC的基本概念:

### 4.数值的机器运算：
#### 1）定点数的运算：
##### 1.定点数的移位运算和摄入操作：
##### 2.定点数的加/减运算；
        溢出概念和辨别方法：
##### 3.定点数的乘/除运算：
        掌握移位乘法及实现逻辑：
#### 2）规格化浮点运算：
##### 1.浮点数的运算方法与流程：
##### 2.浮点运算器的组成及实现：
#### 3）算术逻辑单元ALU：
##### 1.串行加法器、并行加法器和快速进位链：
##### 2.算术逻辑单元ALU的基本组成与实现：

### 5.存储系统和结构：
#### 1）存储器的分类：
#### 2）存储器的层次化结构：
#### 3）半导体随机存取存储器：
##### 1.SRAM存储器的工作原理：
##### 2.DRAM存储器的工作原理：
##### 3.只读存储器：
##### 4.Flash存储器：
#### 4）主存储器与CPU的连接
#### 5）双口RAM和多模块存储器
#### 6）高速缓存存储器（Cache）
##### 1.程序访问的局部：
##### 2.Cache的基本工作原理：
##### 3.Cache和主存之间的映射关系：
##### 4.Cache中主存块的替换算法：
##### 5.Cache写策略：

### 6.中央处理器(CPU)
#### 1）CPU的功能和基本结构：
#### 2）数据通路的功能和基本结构：
#### 3）时序系统与控制方式：
#### 4）指令执行过程：
#### 5）控制器的功能和工作原理：
##### 1.组合逻辑控制器：
##### 2.微程序控制器：微程序、微指令和微命令；微指令的编码方式；位地址的形式方式：
#### 6）控制单元的设计：
        设计模型机的流程：
        组合逻辑控制器的组成原理及设计方法：
        微程序控制器的设计方法与步骤：

### 7.输入输出(I/O)系统
#### 1）I/O系统基本概念：
#### 2）I/O接口（I/O控制器）
##### 1.I/O接口的功能和基本结构：
##### 2.I/O端口及其编址
#### 3）I/O方式：
##### 1.程序查询方式：
##### 2.程序中断方式：
        中断的基本概念：
        中断响应过程：
        中断处理过程：
        多重中断和中断平屏蔽的概念：
##### 3.DMA方式：
        DMA控制器的组成
        DMA传送过程
##### 4.通道方式
#### 4）总线技术
##### 1.总线的基本概念、分类及性能指标
##### 2.同步定时方式，一步定时方式

------------------------------
# 数据结构
## 1.数据结构绪论
### 1）数据结构的基本概念、数据的逻辑结构与物理结构
### 2）算法和算法分析
## 2.线性表
### 1）线性表的定义及其基本操作
### 2）线性表的顺序存储结构
### 3）线性表的链式存储结构
### 4）线性表的应用
## 3.栈和队列
### 1）栈和队列的定义及其操作
### 2）栈和队列的顺序存储结构
### 3）栈和队列的链式存储结构
### 4）栈和队列的应用
## 4.数组
### 1）数组的定义及其操作
### 2）数组的存储结构
### 3）矩阵的压缩存储
## 5.树
### 1）树的基本概念
### 2）二叉树的定义及其基本操作、二叉树的性质与村村结构
### 3）二叉树的遍历
### 4）线索二叉树
### 5）树和森林
### 6）Huffman树与Huffman编码
### 7）二叉树的应用
## 6.图
### 1）图的定义及操作
### 2）图的存储结构
### 3）图的遍历
### 4）最小生成树
### 5）最短路径问题
### 6）拓扑排序与关键路径
### 7）图的应用
## 7.查找
### 1）查找的基本概念
### 2）顺序表的查找
### 3）二叉排序树（或称二叉查找树）和平衡二叉排序树
### 4）Hash表及其查找
### 5）查找算法的应用
## 8.排序
### 1）排序的基本概念
### 2）插入排序：直接插入排序、折半插入排序、链表插入排序、Shell排序
### 3）交换排序：气泡排序、快速排序
### 4）选择排序：直接选择排序、堆选择排序
### 5）二路归并排序
### 6）基数排序
### 7）各种内排序方法的比较
### 8）内排序算法的应用

---

> 作者: [YYT6801](https://blog.yyt6801.top/)  
> URL: https://blog.yyt6801.top/posts/408_learning_notes/  

