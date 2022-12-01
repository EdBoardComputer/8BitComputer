# 8bit Breadboard Computer
Here's my take on a breadboard computer, inspired by Ben Eater's You Tube Series.
* 8 Bit Arithmetic unit, based on 2x 74HC283 for addition and subtraction, plus conventional logic gates for AND, OR, XOR. Two simple hard wired shifters for left and right shift.
* Zero and Carry Flags.
* 8K RAM and 8K ROM. 16 bit memory address bus, 8 bit data bus.
* 16 Bit program counter, 8 bit stack pointer (uses page 1 like 6502cpu)
* Auto incrementing program counter.
* Auto incrementing and decrementing stack pointer on push / pop
* Single 8K control EEPROM
* 3 General purpose registers (A,X,Y) One interrupt / jump vector register (V)
* Conditional jumps and subroutine returns
* Memory Mapped I/O for PS2 Keyboard and 4x20 line LCD display
* Simple interrupt handling
* 270 different op codes
* Runs at about 800Khz
