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

 
