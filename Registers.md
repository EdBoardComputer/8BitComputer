# Registers

There are 13 main registers in the design.

The main registers in the design are A, X, Y, V, H and L.

A, X, Y, and V are accessible via assembly language opcodes. H and L are for internal use within opcodes only. All registers use a single 74HC574 IC as storage.

The input side of each register is connected to the data bus, and can be directly loaded from it. The output side of each register are connected differently.

A Register and H Register, the output side is connected directly to ALUBUSA. A register is selected by default, H will be used if it is loaded during an instruction.
The currently selected ALUBUSA contents can be put back onto the databus via a 74HC245.
X Register and L Register, the output side is connected directly to ALUBUSB. X register is selected by default, L will be used if it is loaded during an instruction.
The currently selected ALUBUSB contents can be put back onto the databus via a 74HC245.

The status of which registers to use is tracked via two flip flops contained in 74HC74. These are set when either the H o L register is loaded, and cleared at the start of a new instruction, or after the ALU output has been read.

The Y register is uses 2 74HC574 ICs. Both are loaded concurrently from the databus. The output of one is connected to the databus, the output of the other is connected to the lower 8 bits of the address bus. This is used unless the Address Low register is loaded during an instruction.

The V regsiter has the input and output sides connected to the address bus.

There is a set of 8 mini LEDs to show the status of ALUBUSA, ALUBUSB and the ALU Output.

Constants Register (K)

This is used internally to place 0, 1 or -1 (0xff) onto the databus, and is used by many different opcodes. It is very simply driven using D6 and D7 of the opcode, feeding an OR gate, the result of which is fed into D0 and an AND gate the result of which is fed into D1-D7, via 74HC245 onto the databus. If D6 and D7 are both 0 (opcodes 0 to 63) 0 is fed onto the databus. For opcodes 64-191 1 is fed onto the databus. For opcodes 192-255 0xff (255) is fed onto the databus.

Programme Counter (PCHI and PCLO)

These are implemented with 4 74HC161 counters, cascaded together so the increment input on the lowest will ripple through the others. The Programme Counter is automatically incremented each time it is output onto the databus. The input sides are connected directly to the databus. Each pair of output sides is connected to the databus via 74HC245 buffer, and to the address bus by a 74HC574 register.

Stack Pointer

This is implemented with 2 74HC193 counters, cascaded together with increment and decrement inputs. The input sides are connected directly to the databus, the output sides to the databus via 74HC245 and the lower address bus via 74HC245 buffer.

Address Low Register

This is an 74HC574 connected between the databus and low 8 bits of the address bus.

Address High Register

This is an 74HC273 connected to the databus, the output feeds the 74HC245 buffer which is connected to the upper 8 bits of the address bus. The 74HC273 reset line is tied to the Instruction register In control, this clears the high address register on each instruction start.

Instruction Register

This is a single 74HC273, the input is connected to the databus, the output is connected to a 74HC574 register. This is then connected to A3 - A10 on the microcode EEPROM. The reset line is driven by logic, which detects microcode instructions containing SR (Forced instruction reset), XI (X Register Input), MI (Memory Input) or AI (A Register Input) are processed.

