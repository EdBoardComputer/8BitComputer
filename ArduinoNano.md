# Arduino Nano
A standard Arduino Nano is used as a boot loader to load the program into RAM. The Arduino can control the following signals
1. Reset Line. This is pulled low at the start of the Arduino sketch. All the 74HC138/238 decoders are disabled, and all the registers used for stack pointer, programme counter and instruction counter are reset to 0.
2. Address High Input. This line allows the high byte of the address register to be loaded from the databus.
3. Address Low Input. This line allows the low byte of the address register to be loaded from the databus.
4. Address Output. This allows the 16 bit address onto the address bus.
5. RAM Write. This allows the RAM to be written from the databus.
6. D0-D7 Databus. These lines are set to output to allow data to be written to either Address Register, or RAM.

Once the program has been loaded, the D0-D7 lines should be set to input, Address High and Low input set to 0, Address Output and RAM Write set to 1. The reset line should be set to 0 and the board will begin execution from address 00.
