# Microcode Operation
One of the original design goals was to use a single 8K EEPROM for the microcode, this has been achieved, with a fair amount of additional logic. Each microcode step is broken into 4 cycles.

1.  Phase T1; Everything is turned off, this lets EEPROM output settle to the next microcode as 74HC193 is incremented at the end of phase T4.
2.  Phase T2; Microcode output is turned on. If a memory output or input is required, the address is loaded to the address bus; else the appropriate register or function is output onto the databus.
3.  Phase T3; If a memory output is required, the data is output onto the databus. The programme counter is incremented.
4.  Phase T4; The data is clocked off the databus, into the appropriate register or memory address. Microcode EEPROM turned off. Finally, the 74HC193 address is incremented. There is also an additional T4s signal which is a short negative going pulse which is used to update the flags.

# Microcode Logic

The microcode is built around the notion that the computer must (at a basic level!) just move information from one place to another. Therefore, there are 16 control lines to put information onto the databus, and 16 control lines to take data from the databus. This fits neatly into a 8 bits. These are decoded directly from the EEPROM using 4 74HC138/74HC238 chips.

The control lines for the data going ON to the bus are as follows :-
```
1. PMO  Programme Counter / Memory Out (Puts next byte onto databus pointed to by the programme counter,
   also increments the programme counter)
2. XLO X or L Register Out. X register onto databus, or L register if previously loaded in the instruction.
3. YO Y Register Out. Y register onto databus
4. CCF Complements Carry Flag
5. SECCLC Sets or Clears Carry flag, depending on D0 of the opcode.
6. PCHO PC High Register Out. Top 8 bits of programme counter to databus.
7. PCLO PC Low Register Out. Bottom 8 bits of programme counter to databus.
8. EO Sum Out. ALU result to databus. Result depends on which operation is selected from the bottom 3 bits of the opcode.
9. SO Add Out. ALU Addition result to databus. May or may not set flags depending on opcode.
10. KO Constants register to databus. Sends 0, 1 or 0xff (-1) depending on opcode.
11. FO Flags Out. Flags to D0,D1 of databus. Also interrupt status on D2, D3.
12. AHO A or H Register Out. A register onto databus, or H register if previously loaded in the instruction.
13. VO Vector Register Out. Vector register onto databus if D0 and D1 are both zero, else Rotate Left, Rotate Right or BIT Instruction.
14. STO Stack Pointer Out. Stack Pointer to databus.
15. MO Memory Out. Reads memory. Y Register is lower 8 bits by default. If Address Low is loaded, this is used. If Address High is loaded, this is used for the upper 8 bits.
16. MTO Stack Out. Stack pointer is decremented. Puts contects of memory pointed to by stack pointer onto databus. 
```
There are also 16 control line for the data to be read FROM the bus as follows :-
```
1.  VI Vector Register from databus
2. XI X Register from databus.
3. YI Y Register from databus.
4. HI H Register from databus.
5. LI L Register from databus.
6. AHI Address High Register from databus. Note this register is reset to 0 at the start of each instruction.
7. ALI Address Low Register from databus. This register is only used once it has been loaded.
8. AI A Register from databus.
9. II Instruction Register from databus.
10. STI Stack Pointer in from databus.
11. PCHI PC High Register in
12. PCLI PC Low Register in
13. FI Flags in (from D0 and D1 only)
14. SR Instruction Reset; no read from databus
15. MI Memory In. Reads memory using AHI for the upper 8 bits (zero if not loaded) and ALI register if loaded, otherwise Y register to lower 8 bits.
16. MTI Stack in. Puts contents at memory pointed to by stack pointer. Stack pointer is then incremented.
```
Each microcode has a maximum total of 8 steps. The 74HC193 step counter is reset to 0 when any instruction containing MI, PCLI, XI, AI or SR is completed.

# Extra Instructions

To go beyond the 256 instruction limit, the wasted steps after all the two step instructions can be accessed, by loading the instruction register twice. Once with 0xff, whose second step is another instruction register load. Hence the extended instruction opcodes are 0xff, followed by the instruction byte.

# Interrupts

These are simply implemented by setting D2 of the data loaded into the 74HC193 step counter at the start of the next instruction to start from step 2 of the first instruction. 
```
1. Step 2 - Flags In / Flags Out. This resets the interrupt flip flop to indicate the interrupt is being serviced, and stops any other interrupts from happening.
2. Step 3 - The lower Program Counter is saved to the stack.
3. Step 4 - The upper Program Counter is saved to the stack.
4. Step 5 - Constants Register (0) is loaded to the upper program counter.
5. Step 6 - The Vector is loaded into Address Lower.
6. Step 7 - The Memory is read into the lower program counter.

This then transfers the program execution to the memory location on zero page, pointed to by the V register.
```
At the end of the interrupt routine, a 'RTI' instruction must be issued to return to the calling position, and allow interuupts again. The steps for this are as follows :-
```
1. Step 0 - Vector In / Vector Out. This sets the interrupt Flip Flop, allowing interrupts to be serviced.
2. Step 1 - The upper Program Counter is loaded from the stack.
3. Step 2 - The lower Program Counter is loaded from the stack.

This transfers the program execution back to the instruction immediately after where the interrupt was triggered.
```

The Interrupt flip flop is reset by the reset line on start up to disable interrupts. They may be enabled with the EI opcode and disabled with the DI opcode.

If the interrupt flag is still valid immediately on completion of this instruction, note that the interrupt will trigger again immediately, without any other opcodes being processed. The interrupt routine must clear the relevent interrupt flag during processing.

