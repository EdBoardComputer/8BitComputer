# Carry Flag

The Carry Flag is an integral part of the ALU processing, and is stored in an 74HC74 Flip Flop.

Each of the possible carry flag results from the ALU are fed into a 74HC174 buffer for storage.

These are :-
```
BITRES - Result of the BIT instruction.
CARRY OUT - Result of the ALU Carry Flag after processing.
BUSAD0 - Bit 0 of ALUBUSA as a result of a shift or rotate right.
BUSAD7 - Bit 7 of ALUBUSA as a result of a shift or rotate left.
```
The outputs from the 74HC174 are fed into 4 74HC125 bus buffers. The enable line of each bus buffer is the required ALU output, and this sends the appropriate carry flag result onto the Carry Flag bus.

There are three additonal inputs to the Carry flag bus, two fed by two further 74HC125 bus buffers, and one by a 74HC367 Bus buffer.
```
From 74HC125
NOT CARRY - to implement complement carry flag
Bit 0 of Opcode - To implement Set or Clear Carry flag, based on opcode.
From 74HC367
D0 - Flags In fed from D0 of the databus.
```
# Zero Flag

The zero flag is driven by a single 8 input NOR gate 4078 attached to the databus.

There are two input buffers for the zero flag, one 74HC125, amd one 74HC367.
```
From 74HC125
Result of NOR gate. Selected unless Flags in is active.
From 74HC367
D1 - Flags in fed from D1 of the databus.
```

# Flag Updating

The Carry flag and Zero flag flip flop is updated from the appropriate bus when any of the following ALU functions are used.
```
BIT Function
ALU OUT (EO) - All ALU functions
SECCLC - Set or Clear Carry Flag
SRL - Shift or rotate left
SRR - Shift or rotate right
FIS - Flags In
SO - Sum Out, but only when bit 5 of the opcode is 0
CCF - Complement Carry Flag.
```
