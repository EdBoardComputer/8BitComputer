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

The lower 3 bits of the instruction are fed into a 74HC138 3-8 line decoder. This gives an active low signal for each of the 8 opcodes.

```
Instruction  S3  S2  S1  S0   M  Carry Flag
ADD           H   L   L   H   L  From Carry
SUB           L   H   H   L   L  From Carry
CMP           L   H   H   L   L  L
OR            H   H   H   L   H  X
XOR           L   H   H   L   H  X
AND           H   L   H   H   H  X
INC           L   L   L   L   L  L
DEC           H   H   H   H   L  H
```
The ALU result is put on the databus by the EO instruction. The result depends on the bottom 3 bits of the opcode.
000 Add (H+L) with Carry. Carry and Zero Flags affected
001 Subtract (H-L) with Borrow (Carry Flag) Carry and Zero Flags affected
010 Compare (Result of H-L, incoming carry ignored, but carry and zero affected by result)
011 Or H or L Carry reset, Zero flag affected.
100 XOR H XOR L Carry reset, Zero flag affected.
101 And H AND L Carry reset, Zero flag affected.
110 Shift / Rotate Right (H Register) Carry, Zero Flags affected.
111 Shift / Rotate Left (H Register) Carry, Zero Flags affected.

The shift / rotate instruction is driven by D7 of the opcode.

The SO instruction is also used to put the sum of H + L onto the databus, but this can be used regardless of opcode. This may or may not update the flags depending on D5 of the opcode. The status of the Carry flag is ignored at the start of the instruction.

# Carry Flag Logic

The carry flag result is driven by the same logic as the ALU operation, by a 74HC151 8 to 1 line multiplexor. 

Inputs 0-2 - Carry Flag Output from ALU
Inputs 3-4 - Set to zero
Input 5 - From CCF logic
Input 6 - ALUBUSAd0 for shift right.
Input 7 - ALUBUSAd7 for shift left.

The output of this is clocked into a 74HC74 latch when the appropriate signal from EO FI or SO is received.

# Zero Flag Logic

This uses a single 74HC4078 8 input NOR gate directly connected to the databus. The output of this is clocked into a 74HC74 latch when the appropriate signal from EO FI or SO is received.

# Flags Reading from Databus

Both the flag inputs and D0/D1 bus signals are routed through a 74HC153 dual 4-1 multiplexor. This allows the FI signal to read the flags directly from the bus, and the SO signal to read the carry flag result direct from the ALU, rather than using the result from the 74HC151 multiplexor.
