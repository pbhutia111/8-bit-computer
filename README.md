# 8-Bit Computer

So far I have watched this entire video:

[![How do computers work? (from scratch, no prior knowledge needed)](https://img.youtube.com/vi/rl0jkP9kOMw/maxresdefault.jpg)](https://www.youtube.com/watch?v=rl0jkP9kOMw)

> **How do computers work? (from scratch, no prior knowledge needed)** — Milen Patel · 11:54:41
> *(click the thumbnail to watch on YouTube)*

📋 **Parts list:** see [BOM.md](BOM.md) for everything I ordered to build it.

And here are my notes I took during that video:

📄 **[Full handwritten notes (PDF)](notes/How-Do-Computers-Work-notes.pdf)** — click to view all 32 pages.

A typed-up version of those notes follows below.

---

## Contents

1. [Binary Numbers](#1-binary-numbers)
2. [Logic Gates](#2-logic-gates)
3. [Full Adders](#3-full-adders)
4. [Sequential Logic](#4-sequential-logic)
5. [RAM](#5-ram)
6. [7-Segment Display](#6-7-segment-display)
7. [EEPROM](#7-eeprom)
8. [Putting it together — the CPU](#8-putting-it-together--the-cpu)

---

## 1. Binary Numbers

- **Binary** = base 2. On its own it only represents positive numbers (each digit is 1 or 0).
- **Binary addition** — add bit by bit, carrying into the next column (just like decimal, but carry happens at 2).
- **Binary subtraction** — instead of subtracting directly, do `A − B = A + (two's complement of B)`. So subtraction becomes addition.

**Unsigned vs signed**
- **Unsigned binary** — only positive numbers.
- **Signed binary** — can represent positive *and* negative numbers. The **leftmost bit is the sign bit**:
  - first bit `0` → number is **positive**
  - first bit `1` → number is **negative** (two's complement)

**Two's complement** is the main way to represent negative binary numbers:
1. Flip all the bits (one's complement)
2. Add 1

That gives the negative version. Most computers use two's complement.

## 2. Logic Gates

| Gate | Operator | Behaves like |
|------|----------|--------------|
| AND | `O = A · B` | 1 only when both inputs are 1 |
| OR | `O = A + B` | 1 when any input is 1 |
| NOT | `O = Ā` | inverts the input |
| NAND | `O = A · B` (inverted) | **the "universal gate"** — every other gate can be built from it |
| NOR | `O = A + B` (inverted) | inverted OR |
| XOR | `O = A ⊕ B` | outputs **1 when the inputs are DIFFERENT** |
| XNOR | `O = A ⊕ B` (inverted) | outputs **1 when the inputs are the SAME** |
| Buffer | `O = I` | passes the input through |

## 3. Full Adders

- **Half adder** — adds two 1-bit numbers. Inputs A, B → **Sum** (XOR) and **Carry** (AND).
- **Full adder** — adds *three* 1-bit inputs (A, B, and carry-in) → Sum + Carry. A full adder = **half adder + half adder + OR gate**.
- **8-bit adder** — chain 8 full adders; each carry-out feeds the next carry-in.

**Subtraction reuses the adder:** `A − B = A + (−B)`. Keep the same adder chain but put an **XOR gate before every B input**. All those XOR gates share one control signal called **SUB**:
- `SUB = 0` → pass B through → **add**
- `SUB = 1` → flip B, and the carry-in `+1` completes two's complement → **subtract**

## 4. Sequential Logic

This is where **memory** begins — it shows up inside D flip-flops/latches, registers, counters, and SRAM/DRAM.

**SR latch** — one of the simplest ways to make memory:

| S | R | Q | Q̄ |
|---|---|---|----|
| 0 | 0 | hold (Qprev) | hold |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 1 | 0 |
| 1 | 1 | 0 | 0 (invalid) |

> Not every memory circuit contains an SR latch, but almost every memory concept starts from the same idea: **feedback that holds a state.**

**D flip-flop** — a **1-bit memory cell**. It stores D into Q only when the clock triggers, usually on the **rising edge**.
- **Latch:** Q follows D the whole time it's enabled.
- **D flip-flop:** Q only updates on the clock edge.

## 5. RAM

Think of RAM like a small table where the CPU can quickly store numbers and read them back later — **a series of addresses and values**.

- **Write(address, data)** — store a value
- **Read(address)** — get the value back

RAM just stores 8-bit patterns; it has no idea what they "mean." The **control unit** decides whether a byte is data or an instruction, based on the instruction cycle. Reading/writing a specific register uses a **mux** (to read one stored byte) and a **demux** (to write to one).

## 6. 7-Segment Display

- A 7-segment display is the little LED display used to show numbers — 7 LED bars (segments) plus order.
- Drive it with an **EEPROM lookup table**: binary input → EEPROM address → EEPROM data pins → resistors → segment pins. Store "which segments light up for each digit" in the EEPROM.

## 7. EEPROM

**ROM (Read-Only Memory)** is meant to be read from, not constantly written into.

| | ROM | RAM |
|---|-----|-----|
| Stores | permanent instructions / data | temporary working data |
| Volatile? | **non-volatile** (keeps data with no power) | **volatile** (lost on power off) |

In an 8-bit computer, ROM holds things that shouldn't disappear when power turns off:
1. Program instructions
2. Microcode / control signals
3. 7-segment lookup table
4. Boot code

**EEPROM** = a type of ROM you can erase electrically (firmware). The lineage: **PROM → EPROM → EEPROM**.

## 8. Putting it together — the CPU

**Control line** — a wire that gives a command (load Register A, output A to the bus, write to RAM, subtract instead of add, increment the program counter, …). A **control word** is all the control lines grouped together — each line decides one thing, and together they tell every part what to do.

**Tri-state buffer** — a gate that either connects a signal to the bus or disconnects it completely (high-impedance). `enable = 1` → C = A; `enable = 0` → C is disconnected. This is what lets many devices share one bus.

**Bus** — a group of shared wires that moves data between parts of the computer. **Rule: only one OUT line active at a time** so a single source drives the bus.

**MAR (Memory Address Register)** — holds the address the computer needs to use. With 16 RAM addresses, the MAR only needs **4 bits** (`0000`–`1111`).

**Clock** — when the clock is **low**, set up the value on the bus; on the **rising edge** it's latched into the register. The bus must be stable *before* the clock edge.

**Program Counter (PC)** — a register holding the **address of the next instruction**. It tells the CPU where to look in RAM and increments after each instruction. A **555 timer** creates the clock "heartbeat."

**Instruction format** — 8 bits = **4-bit opcode + 4-bit operand**. Example `0001 1110`: opcode `0001` (which instruction), operand `1110` (the data or address).

**Instruction set**

| Name | Opcode | Description |
|------|--------|-------------|
| LDA | 0001 | Load: `RegA = RAM[addr]` |
| ADD | 0010 | `RegA = RegA + RAM[addr]` |
| SUB | 0011 | `RegA = RegA − RAM[addr]` |
| LDI | 0101 | Load immediate: `RegA = value` |
| JMP | 0110 | Jump: `PC = addr` |
| JC | 0111 | Jump if carry flag = 1 |
| JZ | 1000 | Jump if zero flag = 1 (else no-op) |
| OUT | 1110 | `OUT = RegA` → display |
| HLT | 1111 | Halt the computer |

**Control unit / microcode** — the control word is a function of the opcode, the step number, and the flags: `CW = f(opcode, step, flags)`. This lookup lives in an EEPROM.
- **Inputs:** opcode (4 bits), step number, zero flag (ZF), carry flag (CF)
- **Outputs (control lines):** HLT, MARin, RAMin, RAMout, IRin, IRout, RegA in/out, SUB, RegB in, OUT, PC increment, PC out, Jump

Each instruction takes a few **clock cycles / microsteps** (T0, T1, T2, T3…). After the last step the step counter **resets to T0** for the next instruction.

**Full block diagram:** Clock Module · Program Counter · MAR · RAM · Instruction Register · Control Logic · A-Register · ALU · B-Register · Output Register — all tied together by the shared **Bus**.

---

## Build plan

1. Start the **Clock Module** (begin with the 555 timer)
2. **A-Register**
3. **Bus**
4. **B-Register**
5. **ALU**
6. **Output Register** + 7-segment display
7. **Program Counter**
8. **RAM** + DIP-switch program input
9. **MAR**
10. **Instruction Register**
11. **Control Unit / microstep counter**
12. **EEPROM programming**

_(Notes transcribed from my handwritten notebook while following the video.)_
