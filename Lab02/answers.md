# Lab 02 Answers
**Student Name:** YOUR NAME HERE

---

## How to decode an instruction encoding

For each question below, find the hex encoding in your disassembly log, convert it to 32-bit binary, then fill in each field using the bit layout diagram.

**Worked Example: `MOVZ X1, #1` (encoding: `d2800021`)**

Step 1 — Convert hex to binary (4 bits per digit):
```
d    2    8    0    0    0    2    1
1101 0010 1000 0000 0000 0000 0010 0001
```

Step 2 — Map bits to the MOVZ field layout:

| 31 | 30-29 | 28-23  | 22-21 | 20-5             | 4-0   |
|----|-------|--------|-------|------------------|-------|
| sf | 10    | 100101 | hw    | imm16            | Rd    |
| 1  | 10    | 100101 | 00    | 0000000000000001 | 00001 |

Step 3 — Identify each field:
- `sf` = 1 → 64-bit register
- `hw` = 00 → no shift (LSL #0)
- `imm16` = 0000000000000001 = 1
- `Rd` = 00001 = X1

---

## Section 3.2 — Interpreting Instruction Encodings

---

**1. `MOVZ X0, #5`**

Hex encoding (from disassembly log): `0x`

Binary (32-bit):
```
d_    _2    9_    f_    f_    f_    _e    5_
_1101 0010 1001   1111  1111 1111   1110 0101
```

| 31 | 30-29 | 28-23  | 22-21 | 20-5  |         4-0 |
|----|-------|--------|-------|-------|        -----|
| sf | 10    | 100101 | hw    | imm16 |         Rd  |
|    |       |        |       |       |             |
   1     10    100101    00  1111111111111111   00101
- `sf` = 1
- `hw` = 00
- `imm16` = 1111111111111111
- `Rd` =  00101

---

**2. `ADD X4, X4, X0`**

Hex encoding (from disassembly log): `0x`

Binary (32-bit):
```
f_    _2    _a    _0    1_    f_    e_    _5
111  0010 1010   0000  0001  1111  1110  0101
```

| 31 | 30 | 29 | 28-24 | 23-22 | 20-16 | 15-10 | 9-5 | 4-0 |
|----|----|----|-------|-------|-------|-------|-----|-----|
| sf | op | S  | 01011 | shift | Rm    | imm6  | Rn  | Rd  |
|  1 |   1| 1  | 01011 |   00  |  11111| 111110|01010|00001|

- `Rm` (binary) = 11111
- `Rn` (binary) = 01010
- `Rd` (binary) = 00001

---

**3. `SUBS X0, X0, X1`**

Hex encoding (from disassembly log): `0x`

Binary (32-bit):
```
9    2    1    e    2    c    a    6
1001 0010 0001 1110 0010 1100 1010 1010 
```

| 31 | 30 | 29 | 28-24 | 23-22 | 20-16 | 15-10 | 9-5 | 4-0 |
|----|----|----|-------|-------|-------|-------|-----|-----|
| sf | op | S  | 01011 | shift | Rm    | imm6  | Rn  | Rd  |
|  1 | 0  |  0 | 01000 |  11   | 00010 |110010 |10010|00110|
Rm = 00010
Rn = 10010
Rd = 00110
Compare the `op` and `S` bits to `ADD` above:
- How does the encoding differ to signal that condition flags should be updated?
The encoding signals update condition flags by setting S = 1
When S = 0, flags are not updated (ADD)
When S = 1, flags are updated (ADDS)

---

**4. `B.NE sum_loop`**

Hex encoding (from disassembly log): `0x`

Binary (32-bit):
```
_b    _2    _1    e_    _2    c_    _a    _7
1011 0010 0001  1110   0010 1100   1010  0111
```

| 31-24    | 23-5  |              4 | 3-0  |
|----------|-------|             ---|------|
| 01010100 | imm19 |              0 | cond |
|          |0001111000101100101 |   |      |

- `imm19` (binary) =
- `imm19` as a two's complement integer = 61797
- Byte offset (imm19 × 4) = 247188
- `B.NE` address (from disassembly) =
- `sum_loop` address (from disassembly) =
- Do they match?

---

## Section 4.1 — Logical Immediate Values

`MOVZ` and `MOVK` each write a 16-bit immediate into one of four slots in a 64-bit register. The `LSL` shift selects which slot:

| bits 63-48 | bits 47-32 | bits 31-16 | bits 15-0 |
|------------|------------|------------|-----------|
| LSL #48    | LSL #32    | LSL #16    | LSL #0    |

`MOVZ` writes the selected slot and **zeros** all others.
`MOVK` writes the selected slot and **keeps** all other bits unchanged.

Use this layout to trace the value of X5 step by step before answering.

---

**X5** (after `MOVZ` + `MOVK`):
`X5 = 0x`

movz = #0xffff (00000000000000ffff)
movk = #0xff lsl #16 = 

**X6** (after `AND X6, X5, #0x00003ffc00003ffc`):
`X6 = 0x`

**X7** (after `ORR X7, X5, #0x00003ffc00003ffc`):
`X7 = 0x`

---

## Section 5 — Instruction Aliases

- What is the base instruction that `CMP X0, X1` translates to?
CMP is just: SUBS XZR, X0,X1
That’s it.
No destination register, no special opcode just a subtract with flags where the result is thrown away.
- What is the full expanded form (including all operands)?
SUBS XZR, X0,X1
XZR = zero register (writes are discarded)

X0 = left operand

X1 = right operand

SUBS = subtract and update flags
