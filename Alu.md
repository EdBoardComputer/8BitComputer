# Arithmetic and Logic Unit

The ALU is based on two 8 bits busses, ALUBUSA and ALUBUSB. ALUBUSA is fed by the A register, or the H register if it has been loaded. ALUBUSB is fed by the X register or the L Rregister if it has been loaded.

The ALU has several ICs with different functions.

The main addition, subtraction and logic operations are carried out by a pair of 74HC181 ALUs.

The shift and rotate instructions are carried out by a pair of 74HC574 registers hard wired to shift left and right.

The BIT instruction is carried out with a 74HC151. One of the 8 bits from ALUBUSA is selected using the lower 3 bits from ALUBUSB

The zero flag is detected by a 4078 IC, which is an 8 input NOR gate, connected to the databus.

The carry flag is set / reset by various signals from the ALU and associated ICs


# ALU Operational Logic

The 74HC181 ALU is a complex IC with more functions available than needed, so some complex logic was required to access the correct function for each instruction.

The lower 3 bits of the opcode are fed into a 74HC138 3-8 line decoder. This gives an active low signal for each of the 8 opcodes. There is also an additonal stand alone sum operation (SO), which can be used by any opcode.

```
Instruction  S3  S2  S1  S0   M  Carry Flag
000 ADD       H   L   L   H   L  From Carry
001 SUB       L   H   H   L   L  From Carry
002 CMP       L   H   H   L   L  L
003 OR        H   H   H   L   H  X
004 XOR       L   H   H   L   H  X
005 AND       H   L   H   H   H  X
006 INC       L   L   L   L   L  L
007 DEC       H   H   H   H   L  H
```
Each of the ALU input signals are derived as follows :-
SUM AND (ADD, SO)
SCI AND (SUB, CMP, INC)
S0 NAND (DEC, OR, SUM)
S1 AND (SUM, INC)
S2 AND (SUM, INC, OR)
S3 AND (SCI, AND)
M  NAND (OR, XOR, AND)

The Carry Input is derived as follows.

CONOFF NAND (ADD, SUB)
CYX AND (CARRY FLAG, CONOFF)
CIN XOR (SCI, CYX)

This forces the Carry Input LOW for SUB, CMP and INC. The Carry Input is taken from the Carry Flip Flop for ADD and SUB.

The Carry Output is further processed as follows to derive the Carry Flag

SCD AND (SUB, CMP, DEC)
COT1 XOR (Carry Out, SCD)
CARRY FLAG AND (COT1, NOT (M) )

The ALU result is put on the databus by the EO instruction. The result depends on the bottom 3 bits of the opcode.
000 Add (ALUBUSA+ALUBUSB) with Carry. Carry and Zero Flags affected
001 Subtract (ALUBUSA-ALUBUSB) with Borrow (Carry Flag) Carry and Zero Flags affected
010 Compare (Result of ALUBUSA-ALUBUSB, incoming carry ignored, but carry and zero affected by result)
011 Or (ALUBUSA OR ALUBUSB) Carry reset, Zero flag set if result 0.
100 XOR (ALUBUSA XOR ALUBUSB) Carry reset, Zero flag set if result 0.
101 And (ALUBUSA AND ALUBUSB) Carry reset, Zero flag set if result 0.
110 Increment (ALUBUSA) Carry flag set on overflow, Zero Flag set if result 0.
111 Decrement (ALUBUSA) Carry flag set on undeflow, Zero Flag set if result 0.

The SO instruction is also used to put the sum of (ALUBUSA + ALUBUSB) onto the databus, but this can be used regardless of opcode. This may or may not update the flags depending on D5 of the opcode. The status of the Carry flag is ignored at the start of the instruction.

# BIT and Shift Instructions

These instructions use the lower 2 bits of the opcode, and are shared with the VO (Vector Register Out) instruction.

00 - V register is placed on the databus.
01 - BIT instruction. BIT taken from ALUBUSA depending on ALUBUSB (0-7) and placed in Carry Flag
10 - Shift or Rotate Right. If bit D7 of the opcode is 0 then 0 is shifted in, else contents of Carry Flag.
11 - Shift or Rotate Left. If bit D7 of the opcode is 0 then 0 is shifted in, else contents of Carry Flag.

