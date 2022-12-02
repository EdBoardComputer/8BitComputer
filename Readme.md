# 8bit Breadboard Computer
Here's my take on a breadboard computer, inspired by Ben Eater's You Tube Series.
* 8 Bit Arithmetic unit, based on 2x 74HC283 for addition and subtraction, plus conventional logic gates for AND, OR, XOR. Two simple hard wired shifters for left and right shift.
* Zero and Carry Flags.
* 8K RAM and 8K EEPROM. 16 bit memory address bus, 8 bit data bus.
* 16 Bit program counter, 8 bit stack pointer (uses memory from 0100-01ff hex like 6502cpu)
* Auto incrementing program counter.
* Auto incrementing and decrementing stack pointer on push / pop
* 8 stage instruction microcode, using single 8K control EEPROM
* 3 General purpose 8 bit registers (A,X,Y) One interrupt / jump vector register (V)
* 2 Internal 8 bit registers (H,L) for ALU input and microcode use only.
* Constants register, giving 0,1 or -1 (0xff)
* Conditional / Unconditional short and long jumps; subroutine returns.
* Combined Decrment/Test/Compare and branch instructions
* Memory Mapped I/O for PS2 Keyboard and 4x20 line LCD display
* Simple interrupt handling
* 8 bit, 16 bit, 8 bit plus offset, 16 bit plus offset, 8 bit pre-decrement and post-increment addressing modes.
* 270 different op codes
* Runs at about 800Khz; 4 cycles per microcode instruction, each instruction 2-8 microcodes, so 8-32 clock cycles.
* Arduino Nano as boot / RAM loader; slow clock for debug
