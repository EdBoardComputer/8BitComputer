# Main Operational Logic
The main operation logic behind the computer is that data is placed onto the databus, from one location, and is then read into another. These steps are repeated as many times as is needed to complete each opcode. There are essentially 5 steps in each transfer cycle.

The main system clock drives a 4017HC Johnson Counter, which provides a positive pulse for each of the 5 steps. The 5th step is effectively not used, as the falling edge of this is the same as the rising edge of the first step. 
Each step has a rising edge and a falling edge. The positive pulses are labelled T1, T2, T3 & T4. Each of these are also inverted to give a negative pulse.

Actions at each step of the Johnson Counter
```
T1 Rising Edge - Buffer for next microcode address loaded from instruction register and step counter.
               - Program Counter loaded to buffer.

NOT T1 Falling Edge - Microcode output disabled. Output Decoders disabled.

NOT T1 Rising Edge - Microcode output enabled. Output Decoders enabled.
                     Address loaded to Address bus if Memory Read or Write is required.

NOT T2 Rising Edge - ALU Output buffers Updated.
                   - Flag Status Updated.
                   - Stack pointer decremented if required.
                   - Instruction Register incremented.

NOT T3 Falling Edge - Input Decoders enabled. Input Reads completed, for 74HC238

NOT T3 Rising Edge - RAM Writes Completed.
                   - Input Decoders disabled
                   - Program Counter incremented if neccessary.

NOT T4 Rising Edge - Stack Pointer Incremented if necessary.
                   - AH and XL Flip Flops Reset so A and X point to ALUBUSA and B respectively, if ALU Read has been done.
