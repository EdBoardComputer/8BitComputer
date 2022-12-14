# Registers

There are 11 main registers in the design.

X, Y, A and V registers are all essentially the same. They utilise a single 74HC574 IC with both sides connected to the databus.

The H and L register are slightly different. They utilise a single 74HC574, with the input side connected to the databus. The output side is connected to the ALUBUSA (H Register) or ALUBUSB (L Register) and to two 74HC245 Buffers which connect back to the databus. There are also a set of 8 mini LED indicators for each bus.

Constants Register (K)

This is used internally to place 0, 1 or -1 (0xff) onto the databus, and is used by many different opcodes. It is very simply driven using D6 and D7 of the opcode, feeding an OR gate, the result of which is fed into D0 and an AND gate the result of which is fed into D1-D7, via 74HC245 onto the databus. If D6 and D7 are both 0 (opcodes 0 to 63) 0 is fed onto the databus. For opcodes 64-191 1 is fed onto the databus. For opcodes 192-255 0xff (255) is fed onto the databus.

Programme Counter (PCHI and PCLO)

These are implemented with 4 74HC193 counters, cascaded together so the increment input on the lowest will ripple through the others. The input sides are connected directly to the databus. Each pair of output sides is connected to the databus via 74HC245 buffer, and to the address bus bya 74HC574 register.

Stack Pointer

This is implemented with 2 74HC193 counters, cascaded together with increment and decrement inputs. The input sides are connected directly to the databus, the output sides to the databus via 74HC245 and the lower address bus via 74HC245 buffers.

Address Low Register

This is an 74HC574 connected between the databus and low 8 bits of the address bus.

Address High Register

This is an 74HC273 connected to the databus, the output feeds the 74HC245 buffer which is connected to the upper 8 bits of the address bus. The 74HC273 reset line is tied to the Instruction register In control, this clears the high address register on each instruction start.

Instruction Register

This is a single 74HC273, the input is connected to the databus, the output is connected to A3 - A10 on the microcode EEPROM. The reset line is driven by the reset latch, which is set when microcode instructions containing SR, XI, MI or AI are processed.

