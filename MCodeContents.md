# MicroCode Contents
The microcode is all contained in a single 8K EEPROM. The microcode is divided into 4 blocks of code, the four blocks are essentially identical except where a change to the flag state nessitates a change to the instruction.
1. Block 0 - All instructions where Carry Flag is Reset and Zero Flag is Reset
2. Block 1 - All instructions where Carry Flag is Reset and Zero Flag is Set
3. Block 2 - All instructions where Carry Flag is Set and Zero Flag is Reset
4. Block 3 - All instructions where Carry Flag is Set and Zero Flag is Set

The instructions are copied into the 4 blocks to make jump handling simpler to implement. The instruction pointer may move between blocks between each microcode step, if the flag status is changed.

Each microcode has 2 parts, location of data onto the bus in the lower nibble, and location of data coming off the bus in the higher nibble.

A sample instruction set for `ADCA(M+Y)`
* `PMO+II`  Read next instruction into instruction register. (This is effectively only ever read from EPROM address 0, but included before each instruction for completeness.
* `PMO+HI`  Read the offset byte into H register. H register selected for ALUBUSA
* YO+LI   Read the Y register into the L register. L register selected for ALUBUSB
* SO+ALI  Read the sum into the Address Register Low. ALUBUSA Reset to A register, ALUBUSB reset to X Register. Memory Bus Low set to Address Register Low.
* MO+LI   Read the memory into the L Register.
* EO+AI   Read the result (sum) into the A register, and reset instruction counter because of AI load.
* Dummy   Spare instruction not used.
