# Main Operational Logic
The main operation logic behind the computer is that data is placed onto the databus, from one location, and is then read into another. These steps are repeated as many times as is needed to complete each opcode. There are essentially 5 steps in each transfer cycle.

The main system clock drives a 4017HC Johnson Counter, which provides a positive pulse for each of the 5 steps. The 5th step is effectively not used, as the falling edge of this is the same as the rising edge of the first step. 
Each step has a rising edge and a falling edge. The positive pulses are labelled T1, T2, T3 & T4. Each of these are also inverted to give ~T1 T2 T3 T4~
