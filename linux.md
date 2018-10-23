##Linux##

##linux库文件加载顺序##
>linux中由**run-time linker** ld.so（或ld-linux.so）负责对程序运行时链接所需要的库文件
>ld.so主要针对a.out二进制文件（旧版）， ld-linux.so主要针对ELF可执行文件（新版）

**加载顺序**
1. 在**DT_RUNPATH**没有设置并且设置了**DT_RPATH**前提下，使用DT_RPATH指定的文件夹搜索库文件**不建议使用**
2. 使用环境变量**LD_LIBRARY_PATH**所指定的路径，除非程序运行在**secure-execution**模式下（该模式下将忽略LD_LIBRARY_PATH参数）
3. 使用**DT_RUNPATH**所指定的路径，该路径下只会搜索**DT_NEEDED**（直接依赖）属性的库文件，库文件自身的依赖文件不会进行搜索加载。（DT_RUNPATH将会搜索依赖树上所有的库文件）
4. 使用缓存文件 **/etc/ld.so.cache** 指定的路径。该文件保存了在之前查询到的库文件路径。然而在程序编译时加入了**-z nodeflib**链接选项时，默认的库文件路径将被忽略。**ldconfig**命令将搜索/etc/ld.so.conf文件指定的路径上、/lib以及/usr/lib所有库文件加入缓存文件ld.so.cache中
5. **/lib**(或/lib64)->**/usr/lib**(或usr/lib64) （**-z nodeflib**链接后将忽略该本搜索路径）


##tty##
>tty这个名称来源于TeleType（电传打字机）

历史上teletype最初的设计目的是用来实时传播股票交易的机器，它由一台打字机，一个打印机以及电缆线组成。通过电缆线，交易所可以实时的将股票价格发送到其他需要的电传打字机器上，电传机器通过打印机将接收到的信息打印出来。

##演进##
最初在那个还不是人人都能使用个人电脑的时代，电脑通常都有多人同时共享使用，个人只是用一个电传机器终端通过串口链接上主机进行工作。

```
										|											 soft ware		  |	
		   <hard ware>                  |    			<kernel>                       |
terminal<--->physical line<--->UART<--->|UART driver<--->line discipline<--->tty driver<--->user process |
(teletype)                              |											   |                |

```

Linux Console的工作方式。通过在软件层对于硬件终端的抽象模拟统一了显示接口

```
                  |                                                 soft ware                               |  
    <hard ware>   |				<kernel>							                   |	
     display<-----|---------------------v                                                 |
    keyboard----->|keyboard driver--->terminal emulator<--->line discipline<--->tty driver<--->user process |
                                        |											     |                 |

```

XTerm的工作方式，通过**pseudo terminal**(伪终端**PTY**)的作用，使原系统更加灵活。pty slave负责模拟文本输入终端硬件，master则模拟终端控制slave进程。
```
<soft ware>
                |--->tty driver(pseudo slave) <---->User process
                v
line discipline- 
                ^
                |--->tty driver(pseudo master) <---->Xterm process

```



##session##