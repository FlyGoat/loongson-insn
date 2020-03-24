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
GPR[RT_0] ← memory[GPR[RS] + offset * 8]
GPR[RT_1] ← memory[GPR[RS] + offset * 8 + 8]
```

The contents of the 128-bit quadword at the memory location specified by the aligned effective address (If the processor doesn't support unaligned access) are fetched and placed in GPR RT_0 and RT_1.

The 8-bit signed offset is multiplied by 8 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 8 + GPR[RS]
if !systemSupportUnaligned && vAddr3..0 ≠ 0 then
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
FPR[RT_0] ← memory[GPR[RS] + offset * 8]
FPR[RT_1] ← memory[GPR[RS] + offset * 8 + 8]
```

The contents of the 128-bit quadword at the memory location specified by the aligned effective address (If the processor doesn't support unaligned access) are fetched and placed in coprocessor 1 general register RT_0 and RT_1.

The 8-bit signed offset is multiplied by 8 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 8 + GPR[RS]
if !systemSupportUnaligned && vAddr3..0 ≠ 0 then
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
memory[GPR[RS] + offset * 8] ← GPR[RT_0]
memory[GPR[RS] + offset * 8 + 8] ← GPR[RT_1]
```
The 128-bit quadword from GPR RT_0 and RT_1 is stored in memory at the location specified by the aligned effective address (If the processor doesn't support unaligned access).

The 8-bit signed offset is multiplied by 8 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 8 + GPR[RS]
if !systemSupportUnaligned && vAddr3..0 ≠ 0 then
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
memory[FPR[RS] + offset * 8] ← GPR[RT_0]
memory[FPR[RS] + offset * 8 + 8] ← GPR[RT_1]
```
The 128-bit quadword from coprocessor 1 general register RT_0 and RT_1 is stored in memory at the location specified by the aligned effective address (If the processor doesn't support unaligned access).

The 8-bit signed offset is multiplied by 8 then added to the contents of GPR RS to form the effective address.

#### Restrictions
An Address Error exception occurs if processor doesn't support unaligned access and
EffectiveAddress3..0 ≠ 0 (not quadword-aligned).

#### Operation
```
vAddr ← sign_extend(offset) * 8 + GPR[RS]
if !systemSupportUnaligned && vAddr3..0 ≠ 0 then
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

---
TODO: gs{l,s}{w,d}{l,r} family, gs{l,s}{b,h,w,d,}x{,c1} family, gs{,d}{mult,div,mod}{,u} family

Not planed: gsle family