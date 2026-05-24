# Reverse Engineering

**Course:** Introduction to Reverse Engineering  
**Level:** Beginner — Module 1  
**Date:** May 23, 2026  

---

## 1. What is Reverse Engineering?

Reverse engineering is the process of analyzing a system (usually compiled software) to understand how it works — **without having access to the original source code**.

**Analogy:** Disassembling a watch to understand its gears — without the original blueprint.

**Key Goal:** Understand:
- What does the program do?
- How does it do it?
- Can we modify it?

**The Reverse Engineering Pipeline:**

---

## 2. Why Reverse Engineering Matters

| Field | Purpose |
|-------|---------|
| **Malware Analysis** | Understand how malicious software operates to build defenses |
| **Vulnerability Research** | Find security flaws before attackers do |
| **Software Interoperability** | Enable compatibility between proprietary and open systems |
| **CTF Competitions** | Solve crackme and binary exploitation challenges |
| **Digital Forensics** | Investigate incidents and recover forensic evidence |
| **Legacy Code Recovery** | Recover functionality from software with no source code |

---

## 3. Ethical & Legal Considerations

**Golden Rule:** Always get explicit written permission before reverse engineering any software you do not own.

###  Legal & Ethical
- Malware you captured yourself
- CTF / crackme challenges
- Your own software
- Open‑source projects
- Licensed bug bounty targets

###  Potentially Illegal
- Commercial software (check ToS)
- DRM circumvention
- Competitor products
- Government systems
- Without owner consent
- 
###  Key Laws
- **CFAA (US)** — Computer Fraud & Abuse Act
- **DMCA §1201** — Anti‑circumvention
- **EU Directive 2016/1148 (NIS)**
- Local cybercrime laws

**Always consult a lawyer.**

---

## 4. Computer Architecture Basics

### CPU & Registers

The CPU is the brain. Registers are its fastest memory slots.

| Register | Purpose |
|----------|---------|
| **EAX** | Accumulator / Return value |
| **EBX** | Base register / General purpose |
| **ECX** | Counter for loops |
| **EDX** | Data / I/O pointer |
| **ESP** | Stack Pointer (top of stack) |
| **EBP** | Base Pointer (stack frame) |
| **EIP** | Instruction Pointer (next instruction) |

### Instruction Execution Cycle

1. **FETCH** — Read instruction from memory (EIP)
2. **DECODE** — CPU decodes the opcode
3. **EXECUTE** — ALU performs the operation
4. **WRITE‑BACK** — Result stored in register/memory
5. **UPDATE EIP** — Point to next instruction

### Memory Layout

| Region | Description |
|--------|-------------|
| **Stack** | LIFO structure, local variables, function calls, fixed size, grows **DOWN** |
| **Heap** | Dynamic allocation (`malloc`/`new`), programmer managed, variable size, grows **UP** |

---

## 5. Binary & Executable Basics (PE File Format)

Windows executables (.exe, .dll) use the **Portable Executable (PE)** format.

| Section | Content |
|---------|---------|
| **DOS Header** | MZ magic bytes, legacy DOS stub |
| **PE Header (COFF)** | Machine type, sections count, timestamp |
| **Optional Header** | ImageBase, EntryPoint, subsystem, imports |
| **.text** | Executable code — what we reverse! |
| **.data** | Initialized global variables, strings |
| **.rdata** | Read‑only data (constants, import table, debug info) |
| **.rsrc** | Resources (icons, dialogs, version info) |

> **Note:** The `MZ` signature is how the OS recognizes a valid Windows executable.

---

## 6. Assembly Language (x86)

### Common Instructions

| Instruction | Example | Meaning |
|-------------|---------|---------|
| `MOV` | `MOV EAX, 5` | Copy value into register or memory |
| `PUSH` | `PUSH EAX` | Put value on top of stack; ESP decreases |
| `POP` | `POP EBX` | Remove top of stack into register |
| `CALL` | `CALL 0x401000` | Call a function (pushes return address) |
| `JMP` | `JMP 0x40108A` | Unconditional jump to address |
| `CMP` | `CMP EAX, 0` | Compare two values (sets flags) |
| `JE` | `JE 0x40108A` | Jump if Equal (ZF=1 after CMP) |
| `JNE` | `JNE 0x40108A` | Jump if Not Equal (ZF=0) |

### Reading Assembly — A Simple Function

**C Source Code (we don’t have this):**

```c
int add(int a, int b) {
    int result = a + b;
    return result;
}
Key points:

    Function Prologue — Every function starts with push ebp / mov ebp, esp

    [ebp+8] = first argument (a)

    [ebp+12] = second argument (b)

    Return value is always in EAX for 32‑bit x86 (stdcall/cdecl)

7. Reverse Engineering Workflow
Step	Name	Description
1	Reconnaissance	File type, size, hash, metadata (file, exiftool)
2	Static Analysis	Strings, imports, disassembly — no execution (Ghidra)
3	Dynamic Analysis	Run in sandbox/VM, set breakpoints (x64dbg)
4	Identify Logic	Find main functions, crypto, network calls, file ops
5	Document	Note addresses, function names, IOCs, behavior
6	Report / Patch	Write report or patch the binary per goal
8. Reverse Engineering Tools
Tool	Type	Description
IDA Pro	Disassembler (commercial)	Industry standard for complex analysis
Ghidra	Disassembler/Decompiler (free)	Best free alternative to IDA (NSA)
x64dbg	Debugger (free)	Modern Windows debugger, great UI
OllyDbg	Debugger (free, 32‑bit)	Classic, legacy malware analysis
Radare2	Framework (free)	CLI power, scriptable, multi‑arch
Binary Ninja	Disassembler (commercial)	Modern UI, great HLIL decompiler
DIE	Identifier (free)	Detect packers, compilers, protectors
PE‑bear	PE Viewer (free)	Inspect PE headers without disassembly
9. Static Analysis Techniques
Technique	Purpose	Example
String Extraction	Find hardcoded URLs, passwords, error messages	strings malware.exe | grep "http"
Import Analysis	See which Windows APIs are used	dumpbin /imports target.exe
Cross References	Find who calls a function or uses a string	Ghidra: Right‑click → References
Control Flow Graph	Visual representation of all code paths	View → Function Graph (Ghidra/IDA)
10. Dynamic Analysis (Debugging Live)

Observe actual execution — see registers change, memory fill, APIs called.

Example (x64dbg):
text

00401020 55              push rbp        ← Breakpoint hit
00401021 48 89 E5        mov rbp, rsp
00401024 48 83 EC        sub rsp, 0x30
00401028 89 7D FC        mov [rbp-0x4], edi
0040102B 8B 45 FC        mov eax, [rbp-0x4]

Registers pane shows real‑time values:

    RAX, RBX, RCX, RDX, RSP, RBP, RIP, RFL

11. Beginner Crackme Walkthrough (Password Check)

Original C code (we don’t have it):
c

int check_password(char* input) {
    if (strcmp(input, "S3cr3t!") == 0)
        return 1;  // ACCESS GRANTED
    return 0;      // ACCESS DENIED
}

What we see in the disassembler:
asm

call strcmp          ; compare strings
test eax, eax        ; test result
jne ACCESS_DENIED    ; jump if NOT equal
; ACCESS GRANTED here

Patching steps:

    Run DIE — identify: not packed, 32‑bit PE, MSVC compiled

    Strings — search for "S3cr3t!" or error messages

    X‑Ref — find all references to the password string

    Find CMP/JNE — locate the conditional jump after strcmp

    Patch — change JNE (0x75) to JE (0x74) — flip the logic

    Test — run patched binary — any password grants access

12. Safe Malware Analysis

Rule #1: ALWAYS analyze malware inside a sandboxed, isolated Virtual Machine. Take a snapshot BEFORE execution.
Lab Setup

    Isolated Windows VM (VMware / VirtualBox)

    Snapshot before analysis

    No network (host‑only adapter)

    Shared folders DISABLED

    FakeNet‑NG for network simulation

IOCs to Collect

    File hashes (MD5, SHA256)

    Registry keys created/modified

    Files dropped on disk

    Network C2 addresses / domains

    Mutex names, process names

Common Malware Behaviors

    Persistence (Run keys, services)

    Privilege escalation

    Credential theft

    Lateral movement

    C2 beacon communication

13. Anti‑Reversing Techniques
Technique	Description	Bypass
Packing	Compresses/encrypts the binary (UPX common)	DIE detects it → unpack first
Obfuscation	Junk code, dead code, misleading variable names	Slow analysis — patience
Anti‑Debugging	IsDebuggerPresent, timing checks, exception tricks	Patch checks or use plugins
VM Detection	Checks for VMware/VBox artifacts	Use barebone or masked VMs
Encryption	Strings and payloads decrypted at runtime	Set breakpoints AFTER decryption
Control Flow Flattening	Replaces normal flow with a dispatch table	Use Miasm or Hex‑Rays to deobfuscate
14. Building Your RE Lab
Host Machine

    VMware / VirtualBox Hypervisor

Windows 10 VM

    Ghidra, x64dbg, DIE, PE‑bear, PEstudio

REMnux Linux VM

    Radare2, strings, strace, binwalk

Best Practices

    Always Snapshot — Take VM snapshots before analyzing anything. Revert if infected.

    Host‑Only Network — No internet access.

    No Shared Folders — Malware can escape via these.

    FakeNet‑NG — Simulates network services so malware "thinks" it has internet.

Recommended Tools

    Ghidra (free)

    x64dbg (free)

    CFF Explorer

    DIE

    PEstudio

    Process Monitor

15. Learning Roadmap
BEGINNER (Months 1–3)

    x86 Assembly basics (OpenSecurityTraining)

    Crackmes.one — easy challenges

    Ghidra + x64dbg tutorials

    PE format deep dive

    HTB / TryHackMe RE rooms

INTERMEDIATE (Months 4–9)

    Anti‑analysis techniques

    64‑bit RE and calling conventions

    Malware traffic analysis (PCAP)

    CTF competitions (picoCTF, DEF CON)

    Scripting Ghidra/Radare2 with Python

ADVANCED (Year 2+)

    Kernel‑level malware analysis

    Exploit development + ROP chains

    IDA Pro scripting (IDAPython)

    Firmware / embedded RE

    Vulnerability research & CVE hunting

16. Glossary of Key Terms
Term	Definition
Disassembly	Converting machine code back to assembly language
Decompilation	Converting binary to higher‑level pseudo‑code (C‑like)
Opcode	The numeric code representing a CPU instruction (e.g., 0x55 = PUSH EBP)
Entropy	Measure of randomness — high entropy often indicates packing/encryption
IOC	Indicator of Compromise — observable evidence of malicious activity
Sandbox	Isolated execution environment for safe dynamic analysis
Unpacking	Extracting the original binary from a packed/compressed shell
CFG	Control Flow Graph — visual map of all execution paths in a function
XREF	Cross‑reference — shows all locations that reference a function/data
Shellcode	Raw machine code designed to be injected and executed in memory
ROP	Return‑Oriented Programming — exploitation technique using existing code
NOP	No Operation (0x90) — does nothing; used to neutralize instructions
17. Resources & Further Learning
Courses

    OpenSecurityTraining2 (ost2.fyi) — free x86/x64 RE courses

    Malware Unicorn workshops (malwareunicorn.org)

    SANS FOR610 — Reverse‑Engineering Malware

Practice

    crackmes.one — graded RE challenges

    Hack The Box — Reversing track

    picoCTF / ångstromCTF — RE categories

Books

    Hacking: The Art of Exploitation — J. Erickson

    Practical Malware Analysis — Sikorski & Honig

    The IDA Pro Book — Chris Eagle

Communities

    r/ReverseEngineering (Reddit)

    OpenRCE forum

    Malware Analysis Slack / Discord servers

Tools

    Ghidra (ghidra-sre.org) — free NSA tool

    x64dbg (x64dbg.com) — free debugger

    ANY.RUN (any.run) — online sandbox

Malware Repos

    MalwareBazaar (bazaar.abuse.ch)

    VirusTotal (virustotal.com)

    theZoo GitHub repo

18. Key Takeaways

    RE is a Skill — Build it Daily
    Even 30 minutes of crackme practice compounds over months into mastery.

    Tools ≠ Skills
    Knowing Ghidra doesn't make you a reverse engineer. Understanding assembly does.

    Safety First
    Always use VMs, snapshots, and isolated networks for malware work.

    Document Everything
    Rename functions, add comments, keep notes. RE is 80% documentation.

    Ethics are Non‑Negotiable
    Only analyze software you have permission to examine. Always.
