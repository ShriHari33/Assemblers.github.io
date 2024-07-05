# A Two-Pass SIC-XE assembler

<dl>
<dt>Course Name</dt>
<dd>Algorithmic Problem Solving</dd>
<dt>Course Code</dt>
<dd>23ECSE309</dd>
<dt>Name</dt>
<dd>Shrihari Hampiholi</dd>
<dt>University</dt>
<dd>KLE Technological University, Hubballi</dd>
</dl>

* * *

[comment]: # (> Robust implementation of a Two-Pass Assembler for the SIC/XE architecture)

#### Note:
This page hosts:

1. Pre-requisites
2. Introduction
3. Objectives
4. Design
5. Challenges
6. Future Scope



* * *

### Prerequisites
* Basic understanding of the SIC and SIC/XE architecture.  The staple recommendation to learn this would be the [Leland Beck](https://www.amazon.in/System-Software-Introduction-Systems-Programming/dp/0201423006) book.

* Modern C++ knowledge.  The code is written with features from C++11 and above.  The code is written in a way that it is easy to understand and follow, and is well-commented and structured!.


_Below is an introduction that I feel would be helpful to understand the project on an abstract level.  Feel free to skip to the next section if you are already familiar with the concepts._

### Introduction
* Understanding of the two-pass assembler:
    - An assembler is essentially an algorithm that is used to convert a respective assembly code into the respective machine code.  A two-pass assembler does this exact thing in _2 distinct steps_.  
    The first pass is used to generate the symbol table and the second pass is used to generate the object code.

* Understanding of the SIC/XE architecture:
    - The [SIC/XE](https://en.wikipedia.org/wiki/Simplified_Instructional_Computer#:~:text=There%20is%20also%20a%20more%20complicated%20machine%20built%20on%20top%20of%20SIC%20called%20the%20Simplified%20Instruction%20Computer%20with%20Extra%20Equipment%20(SIC/XE)) architecture is an *extension* of the [SIC](https://en.wikipedia.org/wiki/Simplified_Instructional_Computer) architecture.  The SIC/XE architecture has more features than the SIC architecture.  The SIC/XE architecture supports **more registers**, **more addressing modes**, and **more instructions** than the SIC architecture, making it _more powerful and versatile_ due to the hardware enhancements.

* Understanding of the SIC/XE instructions:
    - The SIC/XE instructions are divided into three categories: 1. Format 1 instructions, 2. Format 2 instructions, and 3. Format 3/4 instructions.  The Format 1 instructions are the simplest instructions.  The Format 2 instructions are the instructions that have two operands.  The Format 3/4 instructions are the instructions that have three operands.

* Understanding of the SIC/XE addressing modes:
    - The SIC/XE addressing modes are divided into five categories: 
        1. Immediate addressing mode
            - This mode is used to specify the value of the operand directly
        2. Direct addressing mode
            -  This mode is used to specify the address of the operand directly
        3. Indirect addressing mode
            - This mode is used to specify the address of the operand indirectly. 
        4. Indexed addressing mode
            - This mode is used to specify the address of the operand indirectly with an index register. 
        <!-- 5. Base-relative addressing mode.  
            - This mode is used to specify the address of the operand indirectly with a base register. -->

* Understanding of the SIC/XE directives:
    - The SIC/XE directives are used to specify the attributes of the program. 
    These are divided into two categories: 
        1. Assembler directives
            - The Assembler directives are used to specify the attributes of the program that are used by the assembler. 
            Examples of such directives are:  `START, END, BYTE, WORD, RESB, and RESW`.
        2. Machine directives.  
            - The Machine directives are used to specify the attributes of the program that are used by the machine.
            Examples of such directives are: `LDA, STA, ADD, SUB, MUL, DIV, and COMP`.

* Understanding of Control Sections:
    - Control Sections allow for the separation of the program into multiple sections that embrace the readability and maintainability of the program. 
    - The Control Section is a feature of the SIC/XE architecture that allows for the separation of the program into multiple sections.  The Linker, at link-time, is responsible to link the multiple sections of the program into a single executable program.


### Objectives
* The primary objective of this project is to implement a robust two-pass assembler for the SIC/XE architecture.
* The secondary objective of this project is to implement a robust linker that can link multiple sections of the program into a single executable program.
* The tertiary objective of this project is to implement a robust loader that can load the executable program into the memory and execute it.


### Design

