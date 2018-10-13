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