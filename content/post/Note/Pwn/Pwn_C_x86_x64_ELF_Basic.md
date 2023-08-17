---
author: Curious
title: "Note : Pwn C x86/x64 ELF - Basic"
date: 2023-08-17
math: true
slug: note-pwn_c_x86_x64_elf-basic
categories:
    - Note
tags:
    - Pwn
---

## Binary
### Assembly
x86 : 
```asm
; test.asm
; nasm -f elf32 test.asm -o test.o
; ld -m elf_i386 test.o -o test
section .data                 ; (for pwntools' asm)
    msg db "Hello World", 0xa ; .ascii "Hello World"
                              ; .byte 0xa

section .text
    global _start

_start: 
    mov eax, 4
    mov ebx, 1
    mov ecx, msg
    mov edx, 12
    int 0x80

    mov eax, 1
    int 0x80
```

x64 : 
```asm
; test.asm
; nasm -f elf64 test.asm -o test.o
; ld test.o -o test
section .data                 ; (for pwntools' asm)
    msg db "Hello World", 0xa ; .ascii "Hello World"
                              ; .byte 0xa

section .text
    global _start

_start:
    mov rax, 1
    mov rdi, 1
    mov rsi, msg
    mov rdx, 12
    syscall

    mov rax, 60
    mov rdi, 0
    syscall
```

### Calling Convention
x86 : 
- system call

| arg1  | arg2  | arg3  | arg4  | arg5  | arg6  | arg7  |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| `eax` | `edx` | `ecx` | `edx` | `esi` | `edi` | `ebp` |

> - Calling Instruction : `int 0x80`
> - Syscall Detail : `man 2 <syscall_name>`
> - [Syscall Table](https://syscalls.w3challs.com/?arch=x86)

- function call

| arg1  |   arg2    |   arg3    |    arg4    |    arg5    | ... |
| ----- | --------- | --------- | ---------- | ---------- | --- |
| stack | stack + 4 | stack + 8 | stack + 16 | stack + 24 | ... |

x64 : 

- system call

| arg1  | arg2  | arg3  | arg4  | arg5  | arg6  | arg7  | arg8  |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| `rax` | `rdi` | `rsi` | `rdx` | `r10` | `r8`  | `r9`  | stack |

> - Calling Instruction : `syscall`
> - Syscall Detail : `man 2 <syscall_name>`
> - [Syscall Table](https://syscalls.w3challs.com/?arch=x86_64)

- function call

| arg1  | arg2  | arg3  | arg4  | arg5  | arg6  | arg7+ |
| ----- | ----- | ----- | ----- | ----- | ----- | ----- |
| `rdi` | `rsi` | `rdx` | `rcx` | `r8`  | `r9`  | stack |

---
## RELRO
- **Partail RELRO** : pointers to `link_map`'s head and `_dl_runtime_resolve()` can be found in `.got.plt + 0x8` and `.got.plt + 0x10`  
- **No RELRO/Full RELRO** : pointer to `link_map`'s head can be found in `r_debug + 0x8`, where pointer to `r_debug` can be found in `.dynamic + 0xc8`

> `link_map` : 
> ```c
> struct link_map
> {
>     ElfW(Addr) l_addr;                // Difference between the address in the ELF file and the addresses in memory.
>     char *l_name;                     // Absolute file name object was found in.
>     ElfW(Dyn) *l_ld;                  // Dynamic section of the shared object.
>     struct link_map *l_next, *l_prev; // Chain of loaded objects.
> 
>     ...
> };
> ```

---
## FMT
- `%<number>$[p/d/x]` : print `<number>` argument use format `[p/d/x]`
- `%<number>$s` : print content of address that (`<number>` argument) point to
- `%<number>$n` : write the length(4 bytes) of the characters that is already displayed on screen to the address `<number>` argument point to
- `%<number>$hn` : same with `%<number>$n` but it write 2 bytes
- `%<number>$hhn` : same with `%<number>$n` but it write 1 byte
- `%<number>c` : print `<number>` of characters to screen

---
## Tools
- `ldd`
  - `ldd <binary>` : find binary's linked file
- `readelf`
  - `readelf -S <binary>` : display the sections' header
- `objdump`
    - `objudmp -t <binary>` : print symbols and their address
    - `objdump -d -M intel <binary>` : print the assembly of binary in intel style
    - `objdump -T <libc> | grep <function_name>` : print `<function_name>`'s offset in `<libc>`
    - `objdump -R <binary>` : print GOT's information
- `strace`
    - `strace <binary>` : trace the system call when `<binary>` run
- `ltrace`
    - `ltrace <binary>` : trace the library call when `<binary>` run
- `checksec`
    - `checksec <binary>` : print the protection of binary
- `ROPgadget`
    - `ROPgadget --binary <binary>` : list all the gadget
    - `ROPgadget --binary <binary> --only "<pattern>"` : list the gadget that match the `<pattern>`. Ex. `ROPgadget --binary <binary> --only "pop|ret"`
    - `ROPgadget --binary <binary> --only "<pattern>" --multibr` : enable multiple branch gadgets
    - `ROPgadget --binary <binary> --string "<string>"` : find `<string>` in `<binary>`
- `one_gadget`
  - `one_gadget <binary>` : print out `<binary>`'s one gadget and it's condition
- `seccomp-tools`
  - `seccomp-tools dump <binary>` : print out the seccomp rules of the `<binary>`
- `gdb`
    - `run` : execute binary
    - `countinue` : continue running binary
    - `start` : execute binary and stop at binary's entry point
    - `disass <function_name>` : disassemble `<function_name>`
    - `break *<address>` : set breakpoint at `<address>`
    - `info breakpoint` : list breakpoint’s informatino
    - `delete <number>` : delete `<number>` breakpoint
    - `info register` : register’s information
    - `x/[number][b/h/w/g][u/d/s/i/x] <address>` : print data of `<address>`, `[number]` is represent to how many group of data you want to print out, `[b/h/w/g]` is represent 1/2/4/8 byte of group size, `[u/d/s/i/x]` is represent `unsigned int` / `decimal` / `string` / `instruction` / `hex`
    - `ni` : next instruction
    - `si` : if the instruction isn’t function call, then `si` is same with `ni`. but if the instruction is function call, then `si` will step into the function
    - `backtrace` or `bt` : print every information of stack frame
    - `set *<address>=<value>` : set `<address>`'s value to `<value>` for 4 bytes, use `set {char/short/long}<address>=<value>` for 1/2/8 bytes
    - `fin` : finish running current function
    - `ctx` : refresh gdb’s page
    - `vmmap` : list the memory layout
    - `codebase` : print the base address of code
    - `libc` : print the base address of libc
    - `tls` ; print the address of `tls`
    - `heapinfo` : print some information of heap
    - `dump memory <file> <start_addr> <end_addr>` : dump memory (`<start_addr>` ~ `<end_addr>`) to `<file>`

