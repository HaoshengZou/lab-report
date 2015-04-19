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
