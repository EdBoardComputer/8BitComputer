# System Reset

The system reset line is controlled by the Arduino NANO, allowing the board to be reset using the reset button on the NANO board. To ensure that the whole board is reset to allow correct start up, there are two reset lines, one normally high (Hot Reset) and one normally low (Cold Reset)

The NANO drives the Cold Reset line directly, this is inverted to give the Hot Reset Line.

The usage of the reset lines are as follows :-
```
Johnson Counter

This requires extra logic, as in normal operation there is always one of the outputs in a 'high' state.
We need to ensure the board starts from the T1 rising edge to operate correctly.
The T1 signal is therefore derived from

T1 AND (Q0, HOT Reset)

This ensures when the reset line is pulled low, T1 will also be low.

We also need to ensure that the counter is reset to the first pulse on start up.
The Counter reset signal is derived from

JCReset OR(Q5, Cold Reset)

This forces a counter reset when the reset line is high, and does not interfere with the normal counter reset during operation.
