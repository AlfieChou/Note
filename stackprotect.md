# canary  随机值  
gcc -fno-stack-protector/-fstack-protector -o canary canary.c  
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space
  
gcc -o alsrtest alsrtest.c
./alsrtest
ASLR status: 0


# dep   非可执行栈
防止程序执行非代码区域的数据，尤其是由于缓冲区溢出等漏洞而注入的恶意代码(通过标记内存页为可执行或不可执行)  
gcc -g -o deptest deptest.c -z noexecstack  
execstack -q deptest  

非可执行栈：DEP可以将栈（通常用于存储返回地址、局部变量和函数参数）标记为不可执行。这意味着即使攻击者通过缓冲区溢出在栈上注入了代码，也无法直接执行，因为栈的内存页是不允许执行的。


# ASLR 随机化地址空间
echo 0 >/proc/sys/kernel/randomize_va_space 0
栈基址随机 无法可靠地预测这些代码片段的位置 限制信息泄露


漏洞举例：  
#include <stdio.h>  
#include <string.h>  
  
void vulnerable_function() {   
    char buffer[10];  
    // 从以下字符串拷贝改为“ABCD”，不会导致溢出  
    strcpy(buffer,"ABCDEFGHIJKLMNOPaaaaaaaaaaaaaaa");  
    printf("The buffer is located at: %p\n", (void*)buffer);  
}  
  
int main() {  
    printf("Stack Canary test program.\n");  
    vulnerable_function();  
    printf("If you see this line, the program continued execution.\n");  
    return 0;  
}  
vulnerable_function 函数声明了一个类型为 char，长度为 10 的数组 buffer。  
使用 strcpy 函数将一个比 buffer 长的字符串复制到 buffer 中。  
由于源字符串 "ABCDEFGHIJKLMNOPaaaaaaaaaaaaaaa" 的长度超过 10，这将导致数组 buffer 发生溢出。   
栈溢出可能会覆盖栈上的其他重要数据，比如返回地址（当函数返回时控制流跳转的地址）。  


函数调用
参数传递：参数推送到调用堆栈上，或者将参数存储在寄存器中。
调用指令：指令会将返回地址（即CALL指令后面的那条指令的地址）推送到栈

函数返回
返回值：特定的位置，如一个寄存器或函数的栈帧。

跳转回调用者：从栈上弹出返回地址并跳转到那个地址，继续执行调用者的代码。

恢复调用者状态：一旦控制权返回到调用者，它可能需要从栈上清除传递给函数的参数（这又取决于调用约定）。