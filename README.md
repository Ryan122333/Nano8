# ⚡ Nano8 — Optimized 8-Instruction Set Architecture

**Nano8 — Optimized 8-Instruction Set Architecture** is a micro-RISC ISA with exactly **8 instructions**.  
It is explicitly optimized to execute the **brainf**k** programming language with maximum hardware efficiency. By utilizing a pure register-to-register execution model with 64 general-purpose registers, any higher-level boolean or arithmetic operation can be emulated completely in software. It is optimized for hardware stream processing where predictable execution times, minimal logic gate counts, and lightweight embedded execution on FPGA fabric are critical.

The ISA uses a clean **3‑bit opcode space** (000–111) within a uniform **16-bit instruction word size**.

---

## ✨ Instruction Categories

### 1. Memory Access
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x4    | 100    | LDR R, addr | Load data from RAM slot (0–1023) into one of the 8 registers. Used for **`,`** input stream hooks. |
| 0x5    | 101    | STR R, addr | Store data from one of the 8 registers into RAM slot (0–1023). Used for **`.`** output stream hooks. |

### 2. Data Movement
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x6    | 110    | MOV Rd, Rs | Copy contents from any of the 64 source registers (`Rs`) to any destination register (`Rd`). |

### 3. Arithmetic
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x2    | 010    | ADD Rd, Rs | `Rd = Rd + Rs` (Flags updated). Natively performs cell value increment **`+`** and pointer shift **`>`**. |
| 0x3    | 011    | SUB Rd, Rs | `Rd = Rd - Rs` (Flags updated). Natively performs cell value decrement **`-`** and pointer shift **`<`**. |

### 4. Control Flow & System
| Opcode | Binary | Mnemonic | Description |
|--------|--------|----------|-------------|
| 0x0    | 000    | JZ Rc, Rt | If `Rc` is zero, copy the address from `Rt` into the Program Counter. Maps natively to **`[`**. |
| 0x1    | 001    | JNZ Rc, Rt | If `Rc` is not zero, copy the address from `Rt` into the Program Counter. Maps natively to **`]`**. |
| 0x7    | 111    | HLT | Halt CPU execution. Shuts down the instruction fetch cycle cleanly when the program terminates. |

---

## 🏷️ Instruction Set (Binary Opcodes)

| Opcode (Binary) | Opcode (Hex) |        Mnemonic      | Description |
|-----------------|--------------|----------------------|-------------|
| `000`           | 0x0          | **JZ Rc, Rt**        | Jump if Zero: Register-indirect branch for forward loop boundaries |
| `001`           | 0x1          | **JNZ Rc, Rt**       | Jump if Not Zero: Register-indirect branch for backward loop boundaries |
| `010`           | 0x2          | **ADD Rd, Rs**       | Add source register to destination register (ALU Flags updated) |
| `011`           | 0x3          | **SUB Rd, Rs**       | Subtract source register from destination register (ALU Flags updated) |
| `100`           | 0x4          | **LDR R_low, addr**  | Load from memory slot (0–1023) into lower register (R0–R7) |
| `101`           | 0x5          | **STR R_low, addr**  | Store lower register (R0–R7) value into memory slot (0–1023) |
| `110`           | 0x6          | **MOV Rd, Rs**       | Move/Copy register contents across all 64 general-purpose registers |
| `111`           | 0x7          | **HLT**              | Halt execution and safely terminate the instruction pipeline |

---

## 📜 Encoding Notes
- **Instruction Width**: Exactly 16 bits uniform word size.
- **Opcode Field**: 3 bits (bits 15–13) selecting one of 8 instructions.
- **Register Fields (`Rd`, `Rs`, `R_check`, `R_target`)**: 6 bits each, supporting 64 general-purpose registers (`R0`–`R63`).
- **Memory Reach (`LDR`/`STR`)**: Constrained to the lower 8 registers (`R0`–`R7`, 3 bits) to leave 10 bits for direct allocation of 1024 slots in RAM.
- **Optimization Chaining**: Successive instruction sequences (such as `+++`) are optimized by the compiler into single-cycle register math instructions (e.g., loading an immediate offset value into a high register and executing `ADD Rd, Rs`), matching native C performance characteristics.

---

## ✅ Purpose
Nano8 is designed as an **ultra-minimalist, pure-RISC execution core** engineered for FPGA deployment, compilation research, and hardware-accelerated processing of the **brainf**k** programming language.
