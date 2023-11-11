---
author: Curious
title: "Note : JMP to Opcode"
date: 2023-11-11
math: true
slug: note-jmp_to_opcode
categories:
    - Note
tags:
    - Pwn
---

## JMP to Opcode
| Instruction | Opcode | Explanation | 
| --- | --- | --- |
| `jmp <相對位址>` | `EA [ ]` | `[ ]` 填入 `<相對位址>`（1 bytes） |
| `jmp <相對位址>` | `E9 [ ] [ ]` | `[ ] [ ]` 填入 `<相對位址>`（2 bytes） |
| `jmp <相對位址>` | `E9 [ ] [ ] [ ] [ ]` | `[ ] [ ] [ ] [ ]` 填入 `<相對位址>`（4 bytes） |

這邊所有的相對位址都是和 `jmp` 執行結束後的 instruction 的相對位址，而且是有號整數

---
## Pwntools
我們可以在 `asm` 中使用 `jmp $+<相對位址>`/`jmp $-<相對位址>` 來指定要跳到的位址，這個相對位址也是相對 `jmp` 下一個 instruction 的 address
