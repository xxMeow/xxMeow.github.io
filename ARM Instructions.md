# Instructions

## condField // A3.2

前4bit为条件码（cond），可用助记符表示，根据CPSR中特定位的值决定是否执行条件

0 - 0000 -> EQ(equal) -> Z set - Z == 1

E - 1110 -> AL(always) -> /

## Branch // A3.3

### B // branch - 1010

### BL // branch and link - 1011

- Preservers the return address in LR(#14)

![屏幕快照 2018-11-02 03.08.37](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 03.08.37.png)

```pseudocode
if ConditionPassed(cond) then
	if L == 1 then
		LR = address of the instruction after the branch instruction
	PC = PC + (SignExtend_30(signed_immed_24) << 2)
```

## Arithmetic // A3.4

![Arithmetic](/Users/xiaomingxian/Typora/Arithmetic.png)

### ADD // addition - 00I0100S // A4.1.3

![屏幕快照 2018-11-02 02.56.49](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 02.56.49.png)

```pseudocode
if ConditionPassed(cond) then
    Rd = Rn + shifter_operand
    if S == 1 and Rd == R15 then
        if CurrentModeHasSPSR() then
        	CPSR = SPSR
        else UNPREDICTABLE
    else if S == 1 then
        N Flag = Rd[31]
        Z Flag = if Rd == 0 then 1 else 0
        C Flag = CarryFrom(Rn + shifter_operand)
        V Flag = OverflowFrom(Rn + shifter_operand)
```

Rd := Rn + shifter_operand

### SUB // subtract - 00I0010S // A.4.1.106

![屏幕快照 2018-11-02 03.01.17](%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-11-02%2003.01.17.png)

```pseudocode
if ConditionPassed(cond) then
	Rd = Rn - shifter_operand
    if S == 1 and Rd == R15 then
    	if CurrentModeHasSPSR() then
        	CPSR = SPSR
    	else UNPREDICTABLE
    else if S == 1 then
        N Flag = Rd[31]
        Z Flag = if Rd == 0 then 1 else 0
        C Flag = NOT BorrowFrom(Rn - shifter_operand)
        V Flag = OverflowFrom(Rn - shifter_operand)
```

Rd := Rn - shifter_operand

### CMP // compare - 00I10101 // A.4.1.15

![屏幕快照 2018-11-02 02.54.40](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 02.54.40.png)

```pseudocode
if ConditionPassed(cond) then
    alu_out = Rn - shifter_operand
    N Flag = alu_out[31]
    Z Flag = if alu_out == 0 then 1 else 0
    C Flag = NOT BorrowFrom(Rn - shifter_operand)
    V Flag = OverflowFrom(Rn - shifter_operand)
```

Update flags after Rn - shifter_operand

### MOV // move - 00I1101S // A.4.1.35

![屏幕快照 2018-11-02 02.59.08](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 02.59.08.png)

```pseudocode
if ConditionPassed(cond) then
	Rd = shifter_operand
    if S == 1 and Rd == R15 then
    	if CurrentModeHasSPSR() then
    		CPSR = SPSR
    	else UNPREDICTABLE
    else if S == 1 then
        N Flag = Rd[31]
        Z Flag = if Rd == 0 then 1 else 0
        C Flag = shifter_carry_out
        V Flag = unaffected
```

Rd := shifter_operand (no first operand)

## Load & Store // A3.11

### LDR // load - 01IPU0W1 // A.4.1.23

![屏幕快照 2018-11-02 03.04.35](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 03.04.35.png)

```pseudocode
MemoryAccess(B-bit, E-bit)
if ConditionPassed(cond) then
	if (CP15_reg1_Ubit == 0) then
		data = Memory[address,4] Rotate_Right (8 * address[1:0])
	else /* CP15_reg_Ubit == 1 */
		data = Memory[address,4]
	if (Rd is R15) then
		if (ARMv5 or above) then
			PC = data AND 0xFFFFFFFE
			T Bit = data[0]
		else
			PC = data AND 0xFFFFFFFC
	else
		Rd = data
```

### STR // store - 01IPU0W1// A.4.1.99

![屏幕快照 2018-11-02 03.05.48](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 03.05.48.png)

```pseudocode
MemoryAccess(B-bit, E-bit)
processor_id = ExecutingProcessor()
if ConditionPassed(cond) then
	Memory[address,4] = Rd
	if Shared(address) then /* from ARMv6 */
		physical_address = TLB(address)
		ClearExclusiveByAddress(physical_address,processor_id,4)
		/* See Summary of operation on page A2-49 */
```

# Registers #R0 - #R15

## The Unbanked Reg

### #0 - #7

refers to the same 32-bit physical register in all processor modes 

## The Banked Reg

### #8 - #14

#14 -> Link Register(#lr)

## PC

### #15

#15 -> Program Counter(#pc)

## PSR: // A2.5

![屏幕快照 2018-11-02 03.22.56](/Users/xiaomingxian/Typora/屏幕快照 2018-11-02 03.22.56.png)

### #CPSR Current Program Status Reg

### #SPSR Saved Program Status Reg

# Addressing Modes





