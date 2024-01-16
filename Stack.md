# 栈溢出攻击 — 利用程序中栈缓冲区溢出漏洞   
输入数据（长于目标缓冲区字符串），覆盖栈上的控制信息  

## 溢出诱导
找缓冲区（局部分配在栈上的数组），并且该程序**允许输入更多数据而不检查长度**而导致溢出。
## 控制流劫持
缓冲区溢出允许攻击者覆盖栈上的返回地址或函数指针等控制流信息。
## 代码注入
攻击者将恶意代码注入程序的内存空间中，并通过改写返回地址或函数指针来执行这段恶意代码。
## 执行恶意代码
控制流被劫持，程序执行流将跳转到攻击者注入的代码处执行，(提升权限、泄露信息或者破坏系统)
  
# 栈保护机制
## Stack Canary 栈保护  
函数的入口处，先从fs/gs寄存器中取出一个4字节(eax)或者8字节(rax)的值存到栈上，当函数结束时会检查这个栈上的值是否和存进去的值一致。

-fstack-protector 启用保护，不过只为局部变量中含有数组的函数插入保护
-fstack-protector-all 启用保护，为所有函数插入保护
-fstack-protector-strong
-fstack-protector-explicit 只对有明确stack_protect attribute的函数开启保护
-fno-stack-protector 禁用保护.

## NX（DEP）（堆栈不可执行）  
将数据所在内存页标识为不可执行，当程序溢出成功转入shellcode时，程序会尝试在数据页面上执行指令，此时CPU就会抛出异常，而不是去执行恶意指令。
-z execstack / -z noexecstack (关闭 / 开启)

## PIE（ASLR）（地址空间分布随机化） 
让程序能装载在随机的地址，从而缓解缓冲区溢出攻击(将程序编译成位置无关，并链接为ELF共享对象)
sudo -s 
echo 2 > /proc/sys/kernel/randomize_va_space
-no-pie / -pie (关闭 / 开启) 


举例  把死循环代码存到数组后覆盖其重要信息   

#include<stdio.h>  

void shellcode( void )  
{  
	while( 1 )  
	    puts("virus run success !");  //死循环  
}  

void target( void )  
{  
    int  a[ 4 ];   
    a[ 6 ] = shellcode;   
    // 把shellcode函数的地址转换为整数后存储到数组索引6的位置。  
    // 然而，数组a只有4个元素（索引从0到3）  
    // 栈上数组a之后的内存可能是函数的返回地址或其他重要的栈帧信息  
    puts("target");  
}  

int main(void)  
{  
    target( );  
    puts("main\n"); // 如果越界写入修改了返回地址，语句永远不会执行
    return 0;  
}  


绕过 GCC 随机数防御机制    
void TarGet( void )    
{     
    int  a[4];               
    int rand = a[4];       // 试图读取数组a的第五个元素的值，但这已经是越界访问，    
    a[ 6 ] = shellcode;    // 越界写入，将shellcode函数的地址写入数组外部，覆盖函数的返回地址。   
    printf("TarGet\n");       
    a[4] = rand;           // 将之前读取到的值恢复到数组a的第五个元素，   
}    
