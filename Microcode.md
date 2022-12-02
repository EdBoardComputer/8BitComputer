# Microcode Operation
One of the original design goals was to use a single 8K EEPROM for the microcode, this has been achieved, with a fair amount of additional logic. Each microcode step is broken into 4 cycles.

1.  Phase T1; Everything is turned off, this lets EEPROM output settle to the next microcode as 74HC193 is incremented at the end of phase T4.
2.  Phase T2; Microcode output is turned on. If a memory output or input is required, the address is loaded to the address bus; else the appropriate register or function is output onto the databus.
3.  Phase T3; If a memory output is required, the data is output onto the databus. The programme counter is incremented.
4.  Phase T4; The data is clocked off the databus, into the appropriate register or memory address. Microcode EEPROM turned off. Finally, the 74HC193 address is incremented. There is also an additional T4s signal which is a short negative going pulse which is used to update the flags.

# Microcode Logic

The microcode is built around the notion that the computer must (at a basic level!) just move information from one place to another. Therefore, there are 16 control lines to put information onto the databus, and 16 control lines to take data from the databus. This fits neatly into a 8 bits. These are decoded directly from the EEPROM using 4 74HC138/74HC238 chips.

The control lines for the data going ON to the bus are as follows :-
1. PMO  Programme Counter / Memory Out (Puts next byte onto bus pointed to by the programme counter, also adds one to programme counter)
2. XO X Register Out. X register onto databus
3. YO Y Register Out. Y register onto databus
4. HO H Regsiter Out. H Register onto databus. This register is tied to one side of the ALU and the two shift / Rotate registers
5. LO L Register Out. L Register onto databus. This register is tied to the other side of the ALU
6. PCHO PC High Register Out. Top 8 bits of programme counter to address bus.
7. PCLO PC Low Register Out. Bottom 8 bits of programme counter to address bus.
8. EO Sum Out. ALU result to databus. Result depends on which operation is selected, ties to bottom 3 bits of the opcode.
9. SO Add Out. ALU Addition result to databus. May or may not set flags depending on opcode.
10. KO Constants register to databus. Sends 0, 1 or 0xff (-1) depending on opcode.
11. FO Flags Out. Flags to D0,D1 of databus. Also interrupt status on D2, D3.
12. AO A Register Out. A register onto databus
13. VO Vector Register Out. Vector register onto databus.
14. STO Stack Pointer Out. Stack Pointer to databus.
15. MO Memory Out. Reads memory using AH and AL registers
16. MTO Stack Out. Stack pointer is decremented. Puts contects of memory pointed to by stack pointer onto databus. 

There are also 16 control line for the data to be read FROM the bus as follows :-
1.  VI Vector Register from databus
2. XI X Register from databus.
3. YI Y Register from databus.
4. HI H Register from databus.
5. LI L Register from databus.
6. ALI Address Low Register from databus
7. AHI Address High Register from databus. Note register is reset to 0 at the start of each instruction.
8. AI A Register from databus.
9. II Instruction Register from databus.
10. STI Stack Pointer in from databus.
11. PCHI PC High Register in
12. PCLI PC Low Register in
13. FI Flags in (from D0 and D1 only)
14. SR Instruction Reset; no read from databus
15. MI Memory In. Reads memory using AH and AL registers
16. MTI Stack in. Puts contents at memory pointed to by stack pointer. Stack pointer is incremented.

Each microcode has a maximum total of 8 steps. The 74HC193 step counter is reset to 0 when any instruction containing MI, PCLI, XI, AI or SR is completed.

# Extra Instructions

To go beyond the 256 instruction limit, the wasted steps after all the two step instructions can be accessed, by loading the instruction register twice. Once with 0xff, whose second step is another instruction register load. Hence the extended instruction opcodes are 0xff, followed by the instruction byte.

# Interrupts

These are simply implemented by changing the data loaded into the 74HC193 step counter at the start of the next instruction to start from step 2. Opcode 0x00 is NOP, which is Loads instruction register as step 0, and reset is step 1. Steps 2-7 Saves the PC HI and PC LO onto the stack, Clears PC high, and puts the vector register into PC LO. It also issues Flags out / flags in (which does nothing, and is not used anywhere else), to stop the interrupt triggering again. The next instruction then resumes from the new address.
