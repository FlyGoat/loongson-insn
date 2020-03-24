# Loongson-EXT Family of instructions

Available on all Loongson64(GS464+) processors.

## Quad word Load & Store
```
|--------------------------------------------------------------------------------
| 31 | 30 | 29 | 28 | 27 | 26 | 25 | 24 | 23 | 22 | 21 | 20 | 19 | 18 | 17 | 16 |    
|           MAJOR_OP          |          RS            |           RT_0         |
|-------------------------------------------------------------------------------- 

---------------------------------------------------------------------|
 15 | 14 | 13 | 12 | 11 | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
 C1 |            offset                  |fmt|         RT_1          |
---------------------------------------------------------------------|
```
|  INSN  |  MAJOR_OP  | C1 | fmt |
|  ----  | ---------- | -- | --- |
|  gslq  | 0x32(LWC2) | 0  |  1  |
| gslqc1 | 0x32(LWC2) | 1  |  1  |
|  gssq  | 0x3A(SWC2) | 0  |  1  |
| gssqc1 | 0x3A(SWC2) | 1  |  1  |

---

### gslq

#### Purpose
To load a quadword from memory to two GPRs

#### Description 
```
GPR[RT_0] ← memory[GPR[RS] + offset * 16]
GPR[RT_1] ← memory[GPR[RS] + offset * 16 + 8]
```

The contents of the 128-bit quadword at the memory location specified by the aligned effective address (If the processor doesn't support unaligned access) are fetched and placed in GPR RT_0 and RT_1.

The 8-bit signed offset in instruction is multiplied by 16 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 16 + GPR[RS]
if !systemSupportUnaligned && vAddr{3..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA) ← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword0 ← LoadMemory(CCA, DOUBLEWORD, pAddr, vAddr, DATA)
memdoubleword1 ← LoadMemory(CCA, DOUBLEWORD, pAddr + 8, vAddr + 8, DATA)
StoreGPR(RT_0, UNINTERPRETED_DOUBLEWORD, memdoubleword0)
StoreGPR(RT_1, UNINTERPRETED_DOUBLEWORD, memdoubleword1)
```

#### Exceptions
Coprocessor Unusable, Reserved Instruction, TLB Refill, TLB Invalid, Address Error

#### Note
Coprocessor Unusable will be emited on GS464 based processors if CU2 is not enabled. 

GS464E and afterwards won't emit Coprocessor Unusable for CU2 in any case.

---

### gslqc1

#### Purpose
To load a quadword from memory to two FPRs

#### Description 
```
FPR[RT_0] ← memory[GPR[RS] + offset * 16]
FPR[RT_1] ← memory[GPR[RS] + offset * 16 + 8]
```

The contents of the 128-bit quadword at the memory location specified by the aligned effective address (If the processor doesn't support unaligned access) are fetched and placed in coprocessor 1 general register RT_0 and RT_1.

The 8-bit signed offset in instruction is multiplied by 16 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 16 + GPR[RS]
if !systemSupportUnaligned && vAddr{3..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA) ← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword0 ← LoadMemory(CCA, DOUBLEWORD, pAddr, vAddr, DATA)
memdoubleword1 ← LoadMemory(CCA, DOUBLEWORD, pAddr + 8, vAddr + 8, DATA)
StoreFPR(RT_0, UNINTERPRETED_DOUBLEWORD, memdoubleword0)
StoreFPR(RT_1, UNINTERPRETED_DOUBLEWORD, memdoubleword1)
```

#### Exceptions
Coprocessor Unusable, Reserved Instruction, TLB Refill, TLB Invalid, Address Error

#### Note
Coprocessor Unusable will be emited on GS464 based processors if CU2 is not enabled. 

GS464E and afterwards won't emit Coprocessor Unusable for CU2 in any case.

---

### gssq

#### Purpose
To store a quadword from two GPRs to memory

#### Description 
```
memory[GPR[RS] + offset * 16] ← GPR[RT_0]
memory[GPR[RS] + offset * 16 + 8] ← GPR[RT_1]
```
The 128-bit quadword from GPR RT_0 and RT_1 is stored in memory at the location specified by the aligned effective address (If the processor doesn't support unaligned access).

The 8-bit signed offset in instruction is multiplied by 16 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 16 + GPR[RS]
if !systemSupportUnaligned && vAddr{3..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA) ← AddressTranslation(vAddr, DATA, STORE)
datadoubleword0 ← GPR[RT_0]
datadoubleword1 ← GPR[RT_1]
StoreMemory(CCA, DOUBLEWORD, datadoubleword0, pAddr, vAddr, DATA)
StoreMemory(CCA, DOUBLEWORD, datadoubleword1, pAddr + 8, vAddr + 8, DATA)
```
#### Exceptions
Coprocessor Unusable, Reserved Instruction, TLB Refill, TLB Invalid, Address Error

#### Note
Coprocessor Unusable will be emited on GS464 based processors if CU2 is not enabled. 

GS464E and afterwards won't emit Coprocessor Unusable for CU2 in any case.

---

### gssq

#### Purpose
To store a quadword from two FPRs to memory

#### Description 
```
memory[FPR[RS] + offset * 16] ← GPR[RT_0]
memory[FPR[RS] + offset * 16 + 8] ← GPR[RT_1]
```
The 128-bit quadword from coprocessor 1 general register RT_0 and RT_1 is stored in memory at the location specified by the aligned effective address (If the processor doesn't support unaligned access).

The 8-bit signed offset in instruction is multiplied by 16 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 16 + GPR[RS]
if !systemSupportUnaligned && vAddr{3..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA) ← AddressTranslation(vAddr, DATA, STORE)
datadoubleword0 ← FPR[RT_0]
datadoubleword1 ← FPR[RT_1]
StoreMemory(CCA, DOUBLEWORD, datadoubleword0, pAddr, vAddr, DATA)
StoreMemory(CCA, DOUBLEWORD, datadoubleword1, pAddr + 8, vAddr + 8, DATA)
```
#### Exceptions
Coprocessor Unusable, Reserved Instruction, TLB Refill, TLB Invalid, Address Error

#### Note
Coprocessor Unusable will be emited on GS464 based processors if CU2 is not enabled. 

GS464E and afterwards won't emit Coprocessor Unusable for CU2 in any case.

### Load & Store with offset

```
|--------------------------------------------------------------------------------
| 31 | 30 | 29 | 28 | 27 | 26 | 25 | 24 | 23 | 22 | 21 | 20 | 19 | 18 | 17 | 16 |    
|           MAJOR_OP          |          RS            |           RT           |
|-------------------------------------------------------------------------------- 

---------------------------------------------------------------------|
 15 | 14 | 13 | 12 | 11 | 10 | 9 | 8 | 7 | 6 | 5 | 4 | 3 | 2 | 1 | 0 |
           RD           |              offset            |    OP0    |
---------------------------------------------------------------------|
```
|  INSN  |  MAJOR_OP  | OP0 |
|  ----  | ---------- | --- |
| gslbx  | 0x36(LDC2) | 0x0 |
| gslhx  | 0x36(LDC2) | 0x1 |
| gslwx  | 0x36(LDC2) | 0x2 |
| gsldx  | 0x36(LDC2) | 0x3 |
| gslwxc1| 0x36(LDC2) | 0x6 |
| gsldxc1| 0x36(LDC2) | 0x7 |
| gssbx  | 0x3E(SDC2) | 0x0 |
| gsshx  | 0x3E(SDC2) | 0x1 |
| gsswx  | 0x3E(SDC2) | 0x2 |
| gssdx  | 0x3E(SDC2) | 0x3 |
| gsswxc1| 0x3E(SDC2) | 0x6 |
| gssdxc1| 0x3E(SDC2) | 0x7 |

---
### gslbx
#### Purpose
To load a byte from memory as a signed value with a offset register

#### Description
``` 
GPR[RT] ← memory[GPR[RS] + GPR[RD] + offset
```
The contents of the 8-bit byte at the memory location specified by the effective address are fetched, sign-extended,
and placed in GPR rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
None

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, BYTE, pAddr, vAddr, DATA)
GPR[rt]← sign_extend(memdoubleword{Bit7...0})
```

#### Exceptions
TLB Refill, TLB Invalid, Address Error, Watch

---

### gslhx

#### Purpose
To load a halfword from memory as a signed value with a offset register

#### Description
``` 
GPR[RT] ← memory[GPR[RS] + GPR[RD] + offset]
```
The contents of the 16-bit halfword at the memory location specified by the effective address are fetched, sign-extended,
and placed in GPR rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned. If the least-significant bit of the address is non-zero, an Address
Error exception occurs.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if vAddr{0...0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, HALFWORD, pAddr, vAddr, DATA)
GPR[rt]← sign_extend(memdoubleword{Bit15...0})
```

#### Exceptions
TLB Refill, TLB Invalid, Address Error, Watch

---
### gslwx

#### Purpose
To load a word from memory as a signed value with a offset register

#### Description
``` 
GPR[RT] ← memory[GPR[RS] + GPR[RD] + offset]
```
The contents of the 32-bit word at the memory location specified by the effective address are fetched, sign-extended, and placed in GPR rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned. If the least-significant bit of the address is non-zero, an Address
Error exception occurs.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if vAddr{1...0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, WORD, pAddr, vAddr, DATA)
GPR[rt]← sign_extend(memdoubleword{Bit31...0})
```

#### Exceptions
TLB Refill, TLB Invalid, Address Error, Watch

---

### gsldx

#### Purpose
To load a word from memory as a signed value with a offset register

#### Description
``` 
GPR[RT] ← memory[GPR[RS] + GPR[RD] + offset]
```
The contents of the 64-bit doubleword at the memory location specified by the effective address are fetched and placed in GPR rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if vAddr{2...0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, DOUBLEWORD, pAddr, vAddr, DATA)
GPR[rt] ← memdoubleword
```

#### Exceptions
TLB Refill, TLB Invalid, Address Error, Watch

---

### gslwxc1

#### Purpose
To load a word from memory as a signed value to a coprocessor 1 general register with a offset register

#### Description
``` 
FPR[RT] ← memory[GPR[RS] + GPR[RD] + offset]
```
The contents of the 32-bit word at the memory location specified by the effective address are fetched, sign-extended, and placed in coprocessor 1 general register rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if vAddr{1...0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, WORD, pAddr, vAddr, DATA)
FPR[rt]← sign_extend(memdoubleword{Bit31...0})
```

#### Exceptions
Coprocessor Unusable, TLB Refill, TLB Invalid, Address Error, Watch

---

### gsldxc1

#### Purpose
To load a word from memory as a signed value to a coprocessor 1 general register with a offset register

#### Description
``` 
FPR[RT] ← memory[GPR[RS] + GPR[RD] + offset]
```
The contents of the 64-bit doubleword at the memory location specified by the effective address are fetched and placed in coprocessor 1 general register rt.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if vAddr{2...0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, LOAD)
memdoubleword← LoadMemory (CCA, DOUBLEWORD, pAddr, vAddr, DATA)
FPR[rt] ← memdoubleword
```

#### Exceptions
Coprocessor Unusable, TLB Refill, TLB Invalid, Address Error, Watch

---
### gssbx

#### Purpose
```
To store a byte to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← GPR[RT]
```

The least-significant 8-bit byte of GPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
None

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← GPR[RT]
StoreMemory (CCA, BYTE, datadoubleword{7...0}, pAddr, vAddr, DATA)
```
#### Exceptions
TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch

---
### gsshx

#### Purpose
```
To store a halfword to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← GPR[RT]
```

The least-significant 16-bit halfword of GPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if !systemSupportUnaligned && vAddr{0..0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← GPR[RT]
StoreMemory (CCA, HALFWORD, datadoubleword{15...0}, pAddr, vAddr, DATA)
```
#### Exceptions
TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch

---
### gsswx

#### Purpose
```
To store a word to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← GPR[RT]
```

The least-significant 32-bit word of GPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if !systemSupportUnaligned && vAddr{1..0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← GPR[RT]
StoreMemory (CCA, WORD, datadoubleword{32...0}, pAddr, vAddr, DATA)
```
#### Exceptions
TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch

---
### gssdx

#### Purpose
```
To store a doubleword to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← GPR[RT]
```

The 64-bit doubleword of GPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if !systemSupportUnaligned && vAddr {2..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← GPR[RT]
StoreMemory (CCA, DOUBLEWORD, datadoubleword, pAddr, vAddr, DATA)
```
#### Exceptions
TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch

---
### gsswxc1

#### Purpose
```
To store a word from a coprocessor 1 general register to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← FPR[RT]
```

The least-significant 32-bit word of FPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if !systemSupportUnaligned && vAddr{1..0} ≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← FPR[RT]
StoreMemory (CCA, WORD, datadoubleword{32...0}, pAddr, vAddr, DATA)
```
#### Exceptions
Coprocessor Unusable, TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch

---
### gssdxc1

#### Purpose
```
To store a doubleword from a coprocessor 1 general register to memory with offset register
```

#### Description
```
memory[GPR[RS] + GPR[RD] + offset] ← FPR[RT]
```

The 64-bit doubleword of FPR rt is stored in memory at the location specified by the effective address.

The 8-bit signed offset in instruction and 64-bit signed offset in GPR RD is added to the contents of GPR RS to form the effective address.

#### Restrictions
The effective address must be naturally-aligned.

#### Operation
```
vAddr ← sign_extend(offset) + GPR[RD] + GPR[RS]
if !systemSupportUnaligned && vAddr {2..0}≠ 0 then
    SignalException(AddressError)
endif
(pAddr, CCA)← AddressTranslation (vAddr, DATA, STORE)
datadoubleword← FPR[RT]
StoreMemory (CCA, DOUBLEWORD, datadoubleword, pAddr, vAddr, DATA)
```
#### Exceptions
Coprocessor Unusable, TLB Refill, TLB Invalid, TLB Modified, Bus Error, Address Error, Watch


---
TODO: gs{l,s}{w,d}{l,r} family, gs{,d}{mult,div,mod}{,u} family

Not planed: gsle family