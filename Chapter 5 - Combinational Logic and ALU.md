$$
\textcopyright \text{Guy Or - 2023}
$$
# Combinational Logic

### Decoder and Encoder
**Decoder** - A logic block that has \$n\$-bit inputs and \$2^n\$ outputs - each corresponding to a single state of the inputs (essentially **decodes** base-2 input into "decimal" output where the decimal number is the "index" of the output (index range \$0 \rightarrow 2^n-1\$.

**Encoder** - A logic block that does the inverse of the Decoder - takes \$2^n\$ inputs and produces the binary representation of them over \$n\$ outputs.

### Multiplexor
A **Multiplexor** AKA **selector** is a device that receives 2 or more **data** inputs and a **selector/control** input. This control input determines which of the data input will be present at the output pin.

The logic equation of a 2-input multiplexor is:
$$
C = (A\cdot \bar{S}) + (B\cdot S)
$$
Where \$A,B\$ are the first and second inputs respectively, \$C\$ is the output signal and \$S\$ is the control signal.

Multiplexors can be of arbitrary number of data inputs.

For the case where there are more than 2 of them, for example \$n\$ inputs (we need \$\lceil log_2(n) \rceil\$ selector inputs) then the Multiplexor is made of:
1) A Decoder that generates \$n\$ **control** signals, each controlling a different input value.
2) An array of \$n\$ AND gates, each combining a single **data** input with its corresponding **control** signal from the decoder.
3) A single large OR gate that combines the outputs of the AND gates.


### Two-level Logic and PLAs

Any logic function can be represented by a canonical form that consists of "2 levels" of AND and OR operations on the inputs (with possible inversions).

The **Sum of Products** - employs AND gates for the first level and combines these terms called **minterms** or **standard products** using an OR gate (the second level).

The **Product of Sums** - employs OR gates for the first level and combines these terms called **maxterms** or **standard sums** using an AND gate (the second level).

For example, let:

$$
\begin{align}
& E \overset{\text{def}}= ((A\cdot B)+(A\cdot C)+ (B\cdot C))\cdot(\overline{A\cdot B\cdot C}) \\
&\\
\Rightarrow
&\begin{cases}
    &SOP(E) = (A\cdot B\cdot \overline{C}) + (A\cdot \overline{B}\cdot C) + (\overline{A}\cdot B\cdot C)\\
    &&\\
    &POS(E) = \overline{(\overline{A}+\overline{B}+C)\cdot(\overline{A}+B+\overline{C})\cdot(A+\overline{B}+\overline{C})}\\
 \end{cases} \\
\end{align}
$$

These representations can be derived from a Truth-table. In specific for SOP, each row where the function is TRUE in the table corresponds to a minterm - where an input with 0 value is inversed. Rows with FALSE generate no terms.

