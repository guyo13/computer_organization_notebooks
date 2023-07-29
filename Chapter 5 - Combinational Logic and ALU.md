$$
\text{© Guy Or - 2023}
$$
# Combinational Logic And Building the ALU

## Combinational Logic
A circuit comprised of *Inputs*, *Outputs* and logic gates, where a change in any of the *Inputs* causes a change in its *Outputs* after a stabilization time denoted \$\Delta T\$.

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

* These representations can be derived from a Truth-table.
* In specific for SOP, each row where the function is TRUE in the table corresponds to a minterm - where an input with 0 value is inversed.
* Rows with FALSE generate no terms.

#### PLA
**PLA** - Programmable logic array, is a logic element (similar to Decoder and Multiplexer) that implements logic functions (that can be defined for example by the ouputs of a Truth-table) using the Sum of Products representations.

The PLA consists of two stages of logic:
1) The **AND Plane** which accepts the inputs and creates minterms (product terms) - using AND gates - for each row in the Truth-table that has a non-zero output. This is the first level of logic in the SOP form of the logic function.
2) The **OR Plane** which accepts the minterms from the AND-Plane and combines them - using OR gates - to create the outputs. This is the second level of logic in the SOP form of the logic function.

There are 2 advantages to this implementation:
1) It can discard any "rows" in the truth-table where all outputs are 0. This is because such rows don't contribute anything to the Sum of Products representation of the logic!
2) Minterms that are found on multiple outputs are "shared" meaning that they are only created once and their signal is used to compute all the outputs that depend on them.  

* The size of the AND Plane in a PLA is given by: \$S_{\text{AND}} = N_{\text{inputs}} \cdot N_{\text{minterms}}\$.
    * Where \$N_{\text{minterms}}\$ is the number of **distinct** minterms (the PLA shares minterms that are found in multiple outputs).
* The size of the OR Plane in a PLA is given by: \$S_{\text{OR}} = N_{\text{outputs}} \cdot N_{\text{minterms}}\$

Finally, the logic that a PLA encodes is fixed at creation time. However there is a component called **PAL** which can be programmed electronically.

### ROMs

**ROM** - Read-only Memory is a logic device that implements electronic memory.

The ROM is conceptually made of:
1) Addressable entries - these are the storage locations of the device. Their number is called the *height*.
2) Input line - used as the signal that selects an addressable entry. If \$\text{height}=2^m\$ then \$N_{\text{inputs}}=m\$.
3) Outputs - These contain the signals corresponding to the value of the selected addressable entry. The number of outputs is called the *width* and is equal to the number of bits in each addressable entry.

The *shape* of A ROM is defined as \$<\text{height},\text{width}>\$ and \$N_{\text{ROM bits}} = \text{height}\cdot \text{width} \$

The ROM **can be used** to implement a set of logic functions by **directly storing their outputs** given a set of inputs - thereby making the ROM a **fully decoded** logic representation. 

This is achieved by storing each row of outputs from the truth-table in the addressable entry corressponding to the input that generated that output.

#### ROM vs. PLA for storing logic functions
As mentioned a ROM is fully decoded and a PLA is partially decoded and therefore a ROM always contains more entries.

Additionally, the **number of entries** - \$N_{\text{entries}}\$ - in the ROM **grows exponentially with the number of inputs** while for most real logic functions \$N_{\text{minterms}}\$ grows much more slowly.

Meaning:
$$
\begin{align}
& N_{\text{entries}} = \Theta(2^{N_{\text{inputs}}}) \\
& N_{\text{minterms}} = o(2^{N_{\text{inputs}}}) \\
\end{align}
$$

**Therefore making PLAs generally more efficient than ROMs to implement Combinational Logic.**
However, ROM contents are easier to change rather than to build a new PLAs making them easier to modify if the logic functions change.

## Constructing a basic ALU

**ALU** - Arithmetic Logic Unit - a combinational logic device that can perform arithmetic operations on inputs.

### 1-Bit ALU
Problem Statement: We want to be able to perform the logical AND/OR operations as well as arithmetic addition between 2 input bits.

* **AND/OR** - Trivial, just use an AND or OR gates respectively.
* **Addition** - We have 3 inputs which are the bits to add \$a,b\$ as well as a *CarryIn* which is useful for chaining multiple adders later on. We have 2 outputs the *Sum* and the *CarryOut*. We derive the Adder implementation by constructing a truth-table, formulating the outputs as SOP, removing don't cares and building it using our choice of logic devices or gates. This type of adder is also called **Full Adder** or **(3,2) Adder**, ommiting the *CarryIn* results in a **(2,2) Adder** or **Half Adder**.

**The 1-Bit ALU** is then constructed by combining these 3 logic devices into a single device (the ALU) that **multiplexes their outputs**.

### 32-Bit ALU
A 32-Bit ALU is constructed by linking 32 1-Bit ALUs. The adder that is constructed this way is called a *ripple carry adder*.

#### Implementing Subtraction
To implement subtraction, we leverage addition with the 2's complement of the subtracted term. To get the 2's complement we:
1) Use a 2:1 Multiplexer to choose between \$b\$  and \$\overline{b}\$. The control line that selects the inversion of \$b\$ is called *Binvert*.
2) Use the *CarryIn* input of the LSB 1-Bit adder to add 1.

This essentially computes the addition of \$a\$ and the 2's complement of \$b\$ (the subtracted).

We can also implement subtraction in practice by taking the XOR of each bit \$b_i\$ with *Binvert* and feeding *Binvert* into the *CarryIn* of the first ALU.

This is an equivalent solution that replaces the 2:1 multiplexors with XOR gates.

#### Implementing NOR

Observe that: \$NOR(a,b) = \overline{a+b} =\overline{a}\cdot \overline{b} \$

We can therefore add an *A-invert* logical step using a multiplexor - same as in the subtraction part - and reuse the AND logic.

#### Implementing slt

*slt* will set all upper 31 bits to 0 and only the LSB can be 1.

1) We will add another multiplexor line and another input line to each 1-Bit ALU, we denote this input line *Less*.
2) For the 31 MSBs we set *Less* to 0.
3) The LSB is defined as: \$ \text{Less} = 1 \Leftrightarrow a-b < 0 \Leftrightarrow \text{Sign bit is 1}\$

Point number 3 is a problem since the *Result* output signal of the **ALU** is not the MSB bit (sign bit) but rather whatever function result the ALU is currently selected by the MUX control.

We solve this problem by defining a special ALU for the 32nd bit:
* A new output signal called *Set* which is the result **of its Adder**.
* A logic circuit for Overflow detection that combines the inputs \$a,b\$ with the Adder result and the *CarryOut*. Its output is exposed from the ALU as *Overflow*.
* Finally *Set* is fed to ALU number 0 *Less* signal.

#### Additional design features
1) For subtracting we want to set *Binvert* and the first *CarryIn* to 1, but for all other operations they should be 0. We therefore combine them to a single control *Bnegate*.
2) For *bne* operation we need to test for the condition \$a-b=0\$ and therefore this is just taking NOR on the subtraction operation result bits. We expose this as the *Zero* output signal of the entire ALU.
3) Finally we get that there are 4 distinct control signal lines: *Ainvert*, *Bnegate* and *Operation* (2 bits).
4) We have in total 3 outputs from the ALU: *Result*, *Zero*, *Overflow* and *CarryOut*.
   * Notice that *Result* is discarded if the operation is *bne*
   * That *Overflow* is directly wired to the 32nd ALU *Overflow* output.
   * *Overflow* is calculated: \$XOR(\text{CarryIn31},\text{CarryOut31})\$

**A major design flaw** with a *ripple carry adder* is the time-consuming sequential evaluation of the carries between the ALUs (this time is linear WRT the number of bits).

A **Carry Lookahead** Adder solves this problem.
