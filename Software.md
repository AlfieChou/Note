# Linux系统 虚拟内存空间区域

## 代码区
编译后程序代码 只读  
## 数据区
全局变量和静态变量  
.data已初始化数据区 .bss未初始化数据区  

## 堆
动态内存分配区域
大小不固定，运行时根据需要进行扩展和收缩
堆区域从低地址向高地址增长

## 栈  
存储局部变量、函数参数、返回地址等。  
栈帧函数调用发生压入，结束弹出  
固定上限，由操作系统在进程启动时设定。  
它从高地址向低地址增长。  


## 堆栈之间的间隙  
堆和栈之间的未使用内存，避免堆的扩展与栈的增长之间的冲突。  

## 内存映射区域  
存放内存映射文件和共享库
进程通过系统调用在这个区域映射文件到内存

## 堆栈顶部的环境变量和命令行参数 
当进程启动时，环境变量和命令行参数被放置在栈的顶部


# 程序产生段错误   
违反了内存保护 程序试图访问它没有权限访问的内存区域，或者访问的地址不合法   
操作系统通过内存管理单元（MMU）来确保每个进程只能访问自己的虚拟内存空间。  

**访问未分配或已释放的内存地址**：操作系统无法找到有效的映射  
**写入只读内存区域**：修改存于代码段的指令，改被标为只读的内存页  
**访问越界内存地址**：超出数组或其他数据结构边界的访问会触及到未被当前进程分配的内存区域，可能会引发段错误。  
**空指针解引用**：NULL指针访问内存 无效内存地址  
**栈溢出**：调用过深 分配过大局部变量  

举例C：  

访问已释放内存地址  
#include <stdlib.h>  
int main() {  
    int *ptr = malloc(sizeof(int)); // 分配内存  
    free(ptr);                      // 释放内存  
    // 这里尝试访问已释放的内存  
    *ptr = 1;  
    return 0;  
} 
  
NULL指针无效访问  
#include <stdio.h>  
int main() {  
    int *ptr = NULL;      
    *ptr = 1;    
}  