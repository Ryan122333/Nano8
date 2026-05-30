# ⚡ Nano8 — Optimized 8-Instruction Set Architecture

**Nano8 — Optimized 8-Instruction Set Architecture** is a micro-RISC ISA with exactly **8 instructions**.  
It is explicitly optimized for highly interactive embedded control and custom application processing. By utilizing a pure register-to-register execution model with 64 general-purpose registers, complex software workflows can be emulated completely via minimal hardware. It is optimized for hardware stream processing, hardware I/O management, and predictable execution times where low logic gate counts and lightweight embedded execution on FPGA fabric are critical.

The ISA uses a clean **3‑bit opcode space** (000–111) within a uniform **16-bit instruction word size**.

---

## ✨ Instruction Categories

### 1. Memory Access
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x4    | 100    | LDR R_low, addr | Load data from RAM slot (0–1023) into one of the lower 8 registers (`R0`–`R7`). |
| 0x5    | 101    | STR R_low, addr | Store data from one of the lower 8 registers (`R0`–`R7`) into RAM slot (0–1023). |

### 2. Peripheral Input/Output
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x1    | 001    | INP Rd, P_addr | Read byte or raw data from an external peripheral/device hardware port directly into destination register `Rd`. |
| 0x3    | 011    | OUT P_addr, Rs | Send data from source register `Rs` out to an external peripheral/device hardware port. |

### 3. Data Movement
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x6    | 110    | MOV Rd, Rs | Copy contents from any of the 64 source registers (`Rs`) to any destination register (`Rd`). |

### 4. Arithmetic
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x2    | 010    | ADD Rd, Rs | `Rd = Rd + Rs` (ALU Flags updated). Subtraction can be cleanly emulated in software via Two's Complement math. |

### 5. Control Flow & System Calls
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x0    | 000    | JZ Rc, Rt | **Jump if Zero:** If `Rc` is zero, copy the address from register `Rt` into the Program Counter. Unconditional branch `JMP` is achieved by pairing `JZ` with a hardwired zero register (`R0`). |
| 0x7    | 111    | TRAP vector | **Trap:** Fire a software interrupt vector to trigger OS or hardware platform subroutines, replacing the restrictive legacy `HLT` execution freeze. |

---

## 🏷️ Instruction Set (Binary Opcodes)

| Opcode (Binary) | Opcode (Hex) |        Mnemonic      | Description |
|-----------------|--------------|----------------------|-------------|
| `000`           | 0x0          | **JZ Rc, Rt** | Jump if Zero: Register-indirect branch for forward loop boundaries (or unconditional branch if using a hardwired zero register). |
| `001`           | 0x1          | **INP Rd, P_addr** | Input: Read data from an external peripheral/device port into destination register `Rd`. |
| `010`           | 0x2          | **ADD Rd, Rs** | Add source register to destination register (ALU Flags updated). |
| `011`           | 0x3          | **OUT P_addr, Rs** | Output: Send data from source register `Rs` out to an external peripheral/device port. |
| `100`           | 0x4          | **LDR R_low, addr** | Load from memory slot (0–1023) into lower register (R0–R7). |
| `101`           | 0x5          | **STR R_low, addr** | Store lower register (R0–R7) value into memory slot (0–1023). |
| `110`           | 0x6          | **MOV Rd, Rs** | Move/Copy register contents across all 64 general-purpose registers. |
| `111`           | 0x7          | **TRAP vector** | Trap: Fire a software interrupt vector to trigger OS/hardware subroutines and system hooks. |

---

## 📜 Encoding Notes
- **Instruction Width**: Exactly 16 bits uniform word size.
- **Opcode Field**: 3 bits (bits 15–13) selecting one of 8 instructions.
- **Register Fields (`Rd`, `Rs`, `Rc`, `Rt`)**: 6 bits each, supporting 64 general-purpose registers (`R0`–`R63`).
- **Memory Reach (`LDR`/`STR`)**: Constrained to the lower 8 registers (`R0`–`R7`, 3 bits) to leave 10 bits for direct allocation of 1024 slots in RAM.
- **The Minimalist ALU**: By eliminating the dedicated hardware subtraction unit (`SUB`), the physical silicon layout is minimized. Subtractions are efficiently executed in software using Two's Complement math (bitwise inversion and adding 1) using the remaining `ADD` instruction.
- **Function Execution Hook**: Function calls and deeply nested call stacks are natively simulated in software by utilizing one general-purpose register as a Stack Pointer (`SP`) and another as a Link Register (`LR`) to manage breadcrumb return paths dynamically through `STR`, `LDR`, and zero-forced `JZ` instructions.

---

## ✅ Purpose
Nano8 is designed as an **ultra-minimalist, pure-RISC I/O execution core** engineered for interactive FPGA deployment, custom ASIC designs, systems compilation research, and safe, low-power embedded control processing.
