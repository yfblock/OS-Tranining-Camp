# Daily Log

## 2022-7-2

今日收到回复，目前已经有一定的rust基础和riscv基础，准备从step1开始，补齐一下riscv系统结构知识。

#### 今日完成事务

1. 构建仓库 局域网git + gitee + github，添加基本ci/cd
2. 完成前期基本工作
3. 收集资料

#### 资料列表

1. osdev: https://wiki.osdev.org/Main_Page

2. rcore Tutorial v3: [rCore-Tutorial-Book-v3 3.6.0-alpha.1 文档](https://rcore-os.github.io/rCore-Tutorial-Book-v3/)

3. 项目要求: 

## 2022-7-3

rust智能指针图

![rust-containers.png](assets-Daily/e5e1f4239aefd1cdcdbece7f4488836714f4cb26.png)

## 今日完成事务

1. 完成lab0-0

2. 重新学习rust智能指针

## 2022-7-4

#### 今日完成事务

1. 完成rustlings

2. 完成test2

## 2022-7-5

#### 今日完成事务

1. 搜索musl libc相关信息

2. 为 自己写的byteos内核加入elf分析函数

## 2022-7-6

#### 今日完成事务

1. 了解内核调用规则 和 tls(线程局部储存) 

2. 搜寻 arg, envp, auxv传递规则资料

```c
argc, argv[0], ..., argv[argc-1], 0, environ[0], ..., environ[N], 0,
auxv[0].a_type, auxv[0].a_value, ..., 0
```

```
------------------------------------------------------------- 0x7fff6c845000
 0x7fff6c844ff8: 0x0000000000000000
        _  4fec: './stackdump\0'                      <------+
  env  /   4fe2: 'ENVVAR2=2\0'                               |    <----+
       \_  4fd8: 'ENVVAR1=1\0'                               |   <---+ |
       /   4fd4: 'two\0'                                     |       | |     <----+
 args |    4fd0: 'one\0'                                     |       | |    <---+ |
       \_  4fcb: 'zero\0'                                    |       | |   <--+ | |
           3020: random gap padded to 16B boundary           |       | |      | | |
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -|       | |      | | |
           3019: 'x86_64\0'                        <-+       |       | |      | | |
 auxv      3009: random data: ed99b6...2adcc7        | <-+   |       | |      | | |
 data      3000: zero padding to align stack         |   |   |       | |      | | |
. . . . . . . . . . . . . . . . . . . . . . . . . . .|. .|. .|       | |      | | |
           2ff0: AT_NULL(0)=0                        |   |   |       | |      | | |
           2fe0: AT_PLATFORM(15)=0x7fff6c843019    --+   |   |       | |      | | |
           2fd0: AT_EXECFN(31)=0x7fff6c844fec      ------|---+       | |      | | |
           2fc0: AT_RANDOM(25)=0x7fff6c843009      ------+           | |      | | |
  ELF      2fb0: AT_SECURE(23)=0                                     | |      | | |
auxiliary  2fa0: AT_EGID(14)=1000                                    | |      | | |
 vector:   2f90: AT_GID(13)=1000                                     | |      | | |
(id,val)   2f80: AT_EUID(12)=1000                                    | |      | | |
  pairs    2f70: AT_UID(11)=1000                                     | |      | | |
           2f60: AT_ENTRY(9)=0x4010c0                                | |      | | |
           2f50: AT_FLAGS(8)=0                                       | |      | | |
           2f40: AT_BASE(7)=0x7ff6c1122000                           | |      | | |
           2f30: AT_PHNUM(5)=9                                       | |      | | |
           2f20: AT_PHENT(4)=56                                      | |      | | |
           2f10: AT_PHDR(3)=0x400040                                 | |      | | |
           2f00: AT_CLKTCK(17)=100                                   | |      | | |
           2ef0: AT_PAGESZ(6)=4096                                   | |      | | |
           2ee0: AT_HWCAP(16)=0xbfebfbff                             | |      | | |
           2ed0: AT_SYSINFO_EHDR(33)=0x7fff6c86b000                  | |      | | |
. . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .        | |      | | |
           2ec8: environ[2]=(nil)                                    | |      | | |
           2ec0: environ[1]=0x7fff6c844fe2         ------------------|-+      | | |
           2eb8: environ[0]=0x7fff6c844fd8         ------------------+        | | |
           2eb0: argv[3]=(nil)                                                | | |
           2ea8: argv[2]=0x7fff6c844fd4            ---------------------------|-|-+
           2ea0: argv[1]=0x7fff6c844fd0            ---------------------------|-+
           2e98: argv[0]=0x7fff6c844fcb            ---------------------------+
 0x7fff6c842e90: argc=3 
```

## 2022-7-7

#### 今日完成事务

1. 将内核添加栈`Stack`管理结构

2. 对内核异常进行统一处理，优化代码结构

## 2022-7-8

#### 今日完成事务

1. 完成`lab1`

2. 搜索aux相关资料

## 2022-7-9

#### 今日完成事务

1. 在`manjaro`上编译`buildroot for linux`，并编译`libc-test`

2. 在本机上调试和在`qemu`上调试发现问题，原来对于映射只是简单的处理，在原文件的基础上映射导致存在未初始化的内存，因此出现问题

## 2022-7-10

#### 今日完成事务

1. 在昨天调试的基础上已经能够进入`__init_tp`

2. 修复部分映射错误问题

3. 目前`byte-os`已经能够进入`main`函数

#### 输出效果

> 目前没有添加参数

```shell
[INFO] 中断号: 96 调用地址: 0x14918
[INFO] 中断号: 64 调用地址: 0x14474
src/common/runtest.c:37: usage: (null) [-t timeoutsec] [-w wrapcmd] cmd [args..]
[INFO] 中断号: 94 调用地址: 0x14be4
[WARN] 未识别调用号 94
[INFO] 中断号: 93 调用地址: 0x14be4
[INFO] 剩余页表: 1421
panic: '已无任务'
```

目前还有`94`中断未支持
