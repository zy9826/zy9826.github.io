# 操作系统基础


<!--more-->
## 操作系统基础
操作系统是直接运行于硬件之上的计算机程序  
操作系统用于管理和控制计算机的硬件与软件资源  
操作系统为用户软件的开发提供必要的服务和接口  


### 系统启动流程
1. 上电
2. 运行BIOS
3. 硬件初始化
4. 建立中断向量表 
5. BIOS最后使命：在存储介质中查找主引导区
6. 找到主引导区后，加载运行主引导程序
7. 软件初始化
8. 加载运行操作系统内核
9. 系统初始化


## BIOS(Base Input Output System)
BIOS是计算机上电后第一个运行的程序（BIOS属于固件）   
BIOS首先检测硬件状态，检测通过后立即进行硬件初始化  
BIOS会在内存中建立中断向量表（提供硬件访问的方法）  
BIOS最后将控制权交由主引导程序执行  

### BIOS是如何被运行起来的？（BIOS的运行机制）
BIOS存储于ROM中，地址映射为0xF0000-0xFFFFF（实地址）  
BIOS的入口地址为：0xFFFF0  
引荐电路的特殊设计使得开机后CPU从0xFFFF0处开始执行    

### BIOS最后的使命
按照用于的设置扫描各个存储介质（光驱，软驱，U盘等）
发现主引导区后，将主引导区中的主引导程序载入内存
主引导程序在内存中的入口地址为0x7c00
将控制权交由主引导程序执行`jmp 0x7c00`

### BIOS如何寻找主引导程序？（主引导区）
位置：位于存储介质的最开始位置处，大小为512字节
特点：前512字节的最后2个有效字节为0x55aa
数据：0x55aa之前的数据被视为主引导程序

汇编基础  
* `$`表示当前指令行地址   
* `$$`表示当前汇编段起始地址  

## 调试环境
### 命令工具
``` shell
# 使用nasm编译
nasm boot.asm -o boot.bin 

# 使用交互方式创建a.img
bximage a.img 

dd if=boot.bin of=a.img bs=512 count=1 conv=notrunc
```

## boot程序:打印字符
``` asm
org 0x7c00

start:
    mov ax,cs
    mov ss,ax
    mov ds,ax
    mov es,ax

    mov si,msg

print:
    mov al,[si]
    add si,1
    cmp al,0x00
    je last
    mov ah,0x0e
    mov bx,0x0f
    int 0x10
    jmp print

last:
    hlt
    jmp last

msg:
    db 0x0a,0x0a
    db "Hello, DTOS!"
    db 0x0a,0x0a
    times 510-($-$$) db 0x00
    db 0x55,0xaa

```

## bochs
### 安装bochs
``` shell
apt install bochs vgabios
which bochs
whereis vgabios
```

### bochs启动方式
1. 显示方式：bochs -f bochsrc_file
2. 隐式方式：bochs 命令执行后会在当前路径查找以下配置文件：.bochsrc bochsrc bochsrc.txt

### bochs启动文件
``` 
# how much memory the emulated machine will have
megs:32

#filename of ROM images
romimage: file=/usr/local/share/bochs/BIOS-bochs-latest
vgaromimage: file=/usr/share/vgabios/vgabios.bin

#what disk images will be used
# floppya: 1_44="freedos.img", status=inserted
floppya: 1_44="data.img", status=inserted

#choose the boot disk
boot: floppy

#where do we send log messages?
#log bochsout.txt

# disable the mouse
mouse: enabled=0

#enable key mapping, usering us layout as default
keyboard: keymap=/usr/local/share/bochs/keymaps/x11-pc-us.map
```

---

> 作者: Yu  
> URL: https://blog.zy9826.site/notebook/cd1c15242598/  

