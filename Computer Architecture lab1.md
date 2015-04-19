# 基于gem5仿真器的单核流水线处理器仿真

## 1.	gem5的安装与测试

网络学堂上提供的gem5-stable源码编译失败，重新在http://repo.gem5.org/gem5-stable
上下载源码，两个压缩包大小确实不一样，新下的源码
编译成功，ALPHA指令集架构下SE模式的测试程序输出正确如下：

```
**** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
Hello world!
Exiting @ tick 3233000 because target called exit()
```

## 2. 交叉编译

### 2.1. 交叉编译器的安装使用

通过`build/ALPHA/gem5.opt configs/example/se.py --cpu-type=minor --l1d_size=8kB --l1i_size=8kB --l1d_assoc=4 --l1i_assoc=4 --l2_size=1MB --l2_assoc=8 --cacheline_size=64 --cpu-clock=1GHz --num-cpus=1`设置参数，通过`build/ALPHA/gem5.opt configs/example/se.py -c queens_alpha -o 8`指定皇后个数为8并运行，得到正确结果92：

```
**** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
8 queens on a 8x8 board...
...there are 92 solutions
Exiting @ tick 287907000 because target called exit()
```

### 2.2.	如何打印输出皇后在棋盘上的位置？简述你的方法并展示结果。

仔细观察网络学堂上提供的queens.c的源码，发现printing和findall参数被人为设定了默认值，对命令行-a、-c的参数读取也与queens.c原作者的设定不符。因此，修改queens.c的源码，使之符合原作者的设定，`queens -a 8`输出八皇后问题的所有解，`queens -c 8`不输出、只统计解的个数，`queens 8`找到第一个解后即输出、退出程序。修改正确后的queens-original.c代码作为报告附件一并提交。

修改源码、交叉编译后，`build/ALPHA/gem5.opt configs/example/se.py -c queens_alpha -o 8`将按原作者预期地输出第一个解后退出：

```
**** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
8 queens on a 8x8 board...
 Q - - - - - - -
 - - - - Q - - -
 - - - - - - - Q
 - - - - - Q - -
 - - Q - - - - -
 - - - - - - Q -
 - Q - - - - - -
 - - - Q - - - -
Exiting @ tick 32866500 because target called exit()
```

`build/ALPHA/gem5.opt configs/example/se.py -c queens_alpha -o "-a 4"`也能输出四皇后问题的全部两个解：

```
**** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
4 queens on a 4x4 board...

Solution #1:
 - Q - -
 - - - Q
 Q - - -
 - - Q -

Solution #2:
 - - Q -
 Q - - -
 - - - Q
 - Q - -

...there are 2 solutions
Exiting @ tick 16860500 because target called exit()
```

### 2.3.	回答：交叉编译的目的是什么？

将源码编译为可以在不同于编译器运行平台的平台上运行的可执行文件。例如：我在基于x86-64处理器（仅支持CISC指令集）的平台上的Linux系统中进行本次实验，但ALPHA是RISC指令集，只能在支持RISC的处理器平台上执行。那么，我就需要在CISC（x86指令集）平台上，将queens.c交叉编译为可以在RISC（ALPHA指令集）平台上执行的文件。如此，用gem5仿真的ALPHA指令集平台就能运行交叉编译的程序了。


## 3.	流水线处理器的性能分析

### 3.1.	Minor流水线结构

