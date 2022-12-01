# Arithmetic and Logic Unit

The ALU is fed via 2 8 bit internal registers named H and L. These are only used internally, their contents is undefined between instructions. The 'H' registers feeds ALUBUSA and the 'L' register feeds ALUBUSB.

The two ALU busses are connected to two 74HC283 ICs for addition. Subtraction is via 2's complement arithmetic, using 8 off XOR gates as per Ben Eaters 8 bit computer. AND, OR and XOR logic is provided by banks of the appropriate gates, connected to the databus via 74HC245 bus drivers. The shift and rotate left and right instructions just hard wire the ALUBUSA across to the appropriate bits along with the optional carry bit or zero, depending on the opcode.

# Operational Logic and Flags

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
