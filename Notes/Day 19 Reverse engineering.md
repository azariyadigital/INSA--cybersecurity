# Reverse Engineering 

What i researched by my self.

**Date:** May 23, 2026  
**Topic:** Introduction to Reverse Engineering (CTF preparation)  
**Goal:** Understand how compiled binaries work, how to analyze them, and how to find hidden flags.

---

## 1. What is Reverse Engineering?

Reverse engineering is the process of taking a compiled binary (no source code) and analyzing it to understand its logic, behavior, and structure.

**Analogy:** Like taking apart a closed watch to figure out how its gears work — without the original blueprint.

---

## 2. Key Concepts Learned

### 2.1 Computer Architecture Basics
- **CPU Registers:** `EAX`, `EBX`, `ECX`, `EDX`, `ESP`, `EBP`, `EIP`
- **Stack:** LIFO structure, grows downward, holds local variables and return addresses
- **Heap:** Dynamic memory, grows upward, managed by programmer

### 2.2 x86 Assembly Instructions

| Instruction | Meaning |
|-------------|---------|
| `MOV` | Copy data |
| `PUSH` / `POP` | Stack operations |
| `CALL` | Call a function |
| `JMP` | Unconditional jump |
| `CMP` | Compare two values |
| `JE` / `JNE` | Conditional jumps (Equal / Not Equal) |

### 2.3 Binary File Structure (PE / ELF)
- `.text` – executable code
- `.data` – initialized global variables
- `.rodata` – read‑only data (strings, constants)
- `.bss` – uninitialized data

### 2.4 Tools for Reverse Engineering

| Tool | Purpose |
|------|---------|
| `file` | Identify binary type |
| `strings` | Extract readable text |
| `objdump` | Disassemble binary |
| `ltrace` / `strace` | Trace library / system calls |
| `gdb` | Debugger (breakpoints, memory inspection) |
| `xxd` | Hex dump |
| `python3` | Payload generation, XOR decoding |

---

## 3. Reverse Engineering Workflow

1. **Reconnaissance** – `file`, `strings`, `ltrace`
2. **Static Analysis** – disassemble with `objdump` or Ghidra
3. **Dynamic Analysis** – run in debugger, set breakpoints
4. **Identify Logic** – find `strcmp`, conditional jumps, VM loops
5. **Document** – note addresses, function behavior, suspicious strings
6. **Extract Flag** – patch binary, decode XOR, or input correct value

---

## 4. Hands‑on Techniques Used

### 4.1 Finding Fake Flags
```bash
strings oracle | grep -i "adwa"
