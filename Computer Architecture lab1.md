# 基于gem5仿真器的单核流水线处理器仿真

## 1.	gem5的安装与测试

网络学堂上提供的gem5-stable源码编译失败，重新在http://repo.gem5.org/gem5-stable上下载源码，两个压缩包大小确实不一样，新下的源码
编译成功，ALPHA指令集架构下SE模式的测试程序输出正确如下：

> **** REAL SIMULATION ****
info: Entering event queue @ 0.  Starting simulation...
info: Increasing stack size by one page.
Hello world!
Exiting @ tick 3233000 because target called exit()

