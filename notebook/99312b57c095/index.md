# 汇编代码


<!--more-->

## 编译流程
将代码放在`d:masm`目录下
编译: `masm code.asm`  
链接: `link code.obj`  
运行: `code.exe`  
调试: `debug code.exe`  

## 源程序示例
``` asm
assume cs:codesg
codesg segment
    mov ax,0123h
    mov bx,0456h
    add ax,bx
    add ax,ax

    mov ax,4c00h
    int 21h
codesg ends

end
```
### 伪指令
伪指令是用来辅助编译器编译的, 编译器根据伪指令来进行相关的编译工作. 汇编语言源程序中包含两种指令, 一种是汇编指令, 一种是伪指令. 汇编指令可以被编译成对应的机器码, 而伪指令不能被编译位机器码.  


- segment和ends: 是一对成对使用的伪指令, 他的功能是定义一个段, segement说明一个段开始, ends说明一个段结束, 一个段必须有一个名称来表示. 这是在写可被编译器编译的汇编程序时必须用到的一对伪指令.  
- end: 是一个汇编程序的结束标记, 编译器在编译汇编程序过程中, 如果碰到了伪指令end, 就结束对源程序的编译.  
- assume: 它假设某一段寄存器和程序中的某一个用segment...ends定义的段相关联.  

## loop指令
loop指令用来实现循环功能, cx中存放循环次数.  
格式: loop 标号  
CPU执行loop指令时的操作:  
1. (cx)=(cx)-1
2. 判断cx不为0则转至标号处执行, 如果为0则向下执行
``` asm
; 循环计算2的12次方
assume cs:codesg
codesg segment
    mov ax,2
    mov cx,11
s:
    add ax,ax
    loop s
    mov ax,4c00h
    int 21h
codesg ends
end
```

``` asm
; 计算1到100累加和
assume cs:codesg

codesg segment
    mov cx,100
    mov ax,0
    mov bx,1
    s:
        add ax,bx
        inc bx
        loop s 
    mov ax,4c00h
    int 21h
codesg ends

end
```

## call和ret指令
- call指令将当前的IP压栈后, 转到标号处执行指令.  
相当于:  
    ```
    push IP
    jmp near ptr 标号
    ```
- `call far ptr 标号`实现的是段间转移  
相当于:
    ```
    push CS
    push IP
    jmp far ptr 标号
    ```
- `call word ptr 内存单元地址`转移地址在内存中的call指令
- ret指令, ret指令用栈中的数据, 修改IP的内容, 从而实现近转移  
相当于`pop IP`  
- retf指令, retf指令用栈中的数据, 修改CS和IP的内容, 从而实现远转移. 相当于:  
    ```
    pop IP
    pop CS
    ```

## 将代码, 数据, 和栈放入不同的段
- 每个段地址会按16字节对齐
- 定义数据时十六进制不能以字母开头, 前面得填充0.  
- 定义start指定程序入口, 并用end start表示程序结束
``` asm
assume cs:code, ds:data, ss:stack
data segment
    ; 数据段可以定义一些数据
    db 'hello, world!', 0        ; 定义一个以0结尾的字符串
    db 23h, 45h, 67h, 89h, 0abh  ; 定义一些字节数据
    dw 789h,0abch                ; 定义16进制数据不能以字母开头, 前面填充0
    db 3 dup('abd','def')
data ends

stack segment
    ; 栈段可以定义栈空间   
    db 11h dup(0)  ; 定义11h个字节的栈空间, 实际占用空间20h, 每个段必须是16字节对齐
stack ends

code segment
    ; 代码段开始
    start:
        mov ax,stack  ; 将栈段地址加载到AX寄存器
        mov ss,ax     ; 设置栈段寄存器SS
        mov sp, 20h      ; 设置栈指针SP，指向栈段的顶部

        mov ax,data    ; 将数据段地址加载到AX寄存器
        mov ds,ax     ; 设置数据段寄存器DS

        mov ax, 4c00h  ; 设置返回代码为0
        int 21h        ; 调用DOS中断，结束程序
    code ends

end start
```


## jmp指令
jmp为无条件转移指令, 可以只修改IP, 也可以同时修改CS和IP.  

### 操作符offset
操作符offset在汇编语言中时由编译器处理的符号, 他的功能时取得标号的偏移地址. 
``` asm
assume cs:codesg
codesg segment
start:
    mov ax,bx
    mov si,offset start
    mov di,offset s
    mov dx,cs:[si]
    mov cs:[di],dx
s:
    nop
    nop
    codesg ends
end start
```

### jmp short 标号
段内短转移, 仅修改IP, IP的修改范围为-128~127.  机器码中存放的是相对地址.  
``` asm
assume cs:codesg
codesg segment
start:
    mov ax,0
    mov bx,0
    jmp short s
    add ax,1
s:
    inc ax
    codesg ends
end start
```

### jmp near ptr 标号
段内近转移, 仅修改IP, IP修改范围-32768~32767. 机器码中存放的是相对地址.  

### jmp far ptr 标号
段间转移, 又称为远转移. 机器码中存放的是IP:CS

### jmp word ptr 内存单元地址
段内转移, 内存单元地址处存放着转移的目的偏移地址.  
``` asm
assume cs:codesg
codesg segment
start:
    mov ax,0123h
    mov ds:[0],ax
    jmp word ptr ds:[0]  ; 跳转到ds段的第0个字
    codesg ends
end start
```

### jmp dword ptr 内存单元地址
段间转移, 内存单元地址处存放着两个字的转移地址, 高地址字是目的段地址(CS), 低地址的字是目的偏移地址(IP).  


## 代码示例
## 数据
数据前加上标号可以当作数组使用, 如下面代码所示定义的str1, 可以使用str1[bx]访问.  
和c++数组下标基于数据类型不一样的是,此处的数组相当于地址偏移并不区分数据类型; 也就是说即使str1使用dw定义, [bx]的含义仍然是[str1+bx].   
``` asm
; 计算str1中的最大值
assume cs:code, ds:data

data segment
    str1 db 'hello, world!'
    str2 db '$'
data ends

code segment
start:
    mov ax, data
    mov ds, ax

    mov bx, offset str1 
    mov cx, offset str2
    sub cx, bx
    mov al,0
loop1:  
    cmp al, str1[bx] ; 数据标号当作数组使用
    ja skip1
    mov al, str1[bx]
skip1:
    inc bx
    loop loop1

    mov ah, 4ch
    int 21h

code ends

end start
```

### 打印字符串
- 字符串以'$'为结束,并且占用内存; 不能以0作为结束符, 不能使用转义字符
- 中断向量只以ah有效  
``` asm
assume cs:code, ds:data
data segment
    str1 db 'hello, world!','$'       
data ends

code segment
start:
    mov ax,data
    mov ds,ax
    mov dx, offset str1
    mov ah,09h
    int 21h
    mov ah,4ch
    int 21h
code ends
end start
```

### 字母小写转大写
``` asm
; 字符串小写转大写
assume cs:code, ds:data

data segment
    str1 db 'hello, world!'
    str2 db '$'
    ; ASCII小写字母范围
    min db 97
    max db 122  
data ends

code segment
start:
    mov ax, data
    mov ds, ax

    mov ax, offset str2  ; AX指向字符串的结束地址
    mov cx, ax           ; CX保存字符串的结束地址
    sub cx, offset str1  ; 计算字符串长度
    mov bx, offset str1  ; BX指向字符串的起始地址

s:
    mov al, [bx]        ; 读取字符串中的一个字符

    cmp al, min         ; 检查是否在小写字母范围内
    jb skip             ; 如果小于97（'a'），跳过转换
    cmp al, max         ; 检查是否在小写字母范围内
    ja skip             ; 如果大于122（'z'），跳过转换
    
    and al, 11011111b   ; 将字符转换为大写
    mov [bx], al        ; 并存回原位置
skip:
    inc bx              ; 移动到下一个字符
    loop s              ; 循环直到CX为0

    mov dx, offset str1  ; DX指向字符串的起始地址
    mov ah,09h
    int 21h

    mov ah, 4ch
    int 21h              ; 结束程序

code ends

end start
```

### 斐波那契数列
``` asm
; 计算斐波那契数列
assume cs:code, ds:data

data segment
    fib dw 10 dup(0)
data ends

code segment
start:
    mov ax, data
    mov ds, ax

    mov word ptr fib[0], 1
    mov word ptr fib[2], 1

    mov bx, 2
    mov ax, [bx]
    mov cx, 8
lp1:
    add ax, [bx-2]
    mov word ptr [bx+2], ax
    add bx, 2
    loop lp1

    mov ah,4ch
    int 21h
code ends

end start
```

### 嵌套循环
``` asm
; 嵌套循环
assume cs:code, ds:data, ss:stack

data segment
str1 db 'aaaaabbbbbcccccd','aaaaabbbbbcccccd','aaaaabbbbbcccccd','aaaaabbbbbcccccd'
data ends

stack segment
db 256 dup(0)
stack ends

code segment
start:
    mov ax, data
    mov ds, ax
    mov ax, stack
    mov ss, ax

mov bx, 0 ; i
mov cx, 4 ; i<4
for1:
    ; mov dx, cx  ; save cx
    push cx
    mov si, 0   ; j
    mov cx, 5   ; j<5
    for2:
        mov al, str1[bx+si]
        and al, 11011111b
        mov str1[bx+si], al
        inc si
        loop for2
    
    add bx, 16
    ; mov cx, dx
    pop cx
    loop for1

    mov ax, 4ch
    int 21h
code ends

end start
```


### 冒泡排序
冒泡排序升序最大值右移
``` asm
; 冒泡排序 升序 最大值右移
assume cs:code, ds:data

data segment
    arr dw 438h, 489h, 102h, 204h, 384h, 203h, 398h, 568h, 12h, 50h
data ends

code segment
start:
    mov ax, data
    mov ds, ax
    
    mov cx, 9
for1:
    mov bx, offset arr
    mov dx, cx
    for2:
        mov ax, arr[bx]
        cmp ax, arr[bx+2]
        jbe skip1
        xchg ax, arr[bx+2]
        xchg ax, arr[bx]
    skip1:
        add bx, 2
        loop for2

    mov cx, dx
    loop for1

    mov ah, 4ch
    int 21h
code ends

end start
```

---

> 作者: Yu  
> URL: https://blog.zy9826.site/notebook/99312b57c095/  

