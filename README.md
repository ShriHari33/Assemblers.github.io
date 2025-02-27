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

#### This page hosts:

1. [Pre-requisites](#prerequisites)
2. [Introduction](#introduction)
3. [Objectives](#objectives)
4. [Design](#design)
5. [Business Cases](#business-cases)
6. [Sample Execution](#sample-execution)
7. [Usage](#usage)
8. [Future Scope](#future-scope)
9. [About Me](#about-me)
10. [References](#references)

* * *

# Prerequisites <a name="prerequisites"></a>
* Basic understanding of the SIC and SIC/XE architecture.  The staple recommendation to learn this would be the [Leland Beck](https://www.amazon.in/System-Software-Introduction-Systems-Programming/dp/0201423006) [^7] book.

* Modern C++ knowledge.  The code is written with features from C++11 and above.  The code is written in a way that it is easy to understand and follow, and is well-commented and structured!.


_Below is an introduction that I feel would be helpful to understand the project on an abstract level.  Feel free to skip to the next section if you are already familiar with the concepts._

# Introduction  <a name="introduction"></a>
* <u>Understanding of the two-pass assembler</u>:
    - An assembler is essentially an algorithm that is used to _convert_ a respective **assembly program** into the respective **machine code**.  A two-pass assembler does this exact thing in _2 distinct steps_.  
    The first pass is used to _generate the symbol table_ and the second pass is used to _generate the object code_ using the symbol table that was generated in the first pass.

* <u>Understanding of the SIC/XE architecture</u>:
    - The [SIC/XE](https://en.wikipedia.org/wiki/Simplified_Instructional_Computer#:~:text=There%20is%20also%20a%20more%20complicated%20machine%20built%20on%20top%20of%20SIC%20called%20the%20Simplified%20Instruction%20Computer%20with%20Extra%20Equipment%20(SIC/XE)) architecture is an *extension* of the [SIC](https://en.wikipedia.org/wiki/Simplified_Instructional_Computer) architecture.  The SIC/XE architecture has more features than the SIC architecture.  The SIC/XE architecture supports **more registers**, **more addressing modes**, and **more instructions** than the SIC architecture, making it _more powerful and versatile_ due to the hardware enhancements.

* <u>Understanding of the SIC/XE instructions</u>:
    - The SIC/XE instructions are divided into three categories:  
    1. Format 1 instructions 
    2. Format 2 instructions
    3. Format 3/4 instructions  
    The Format 1 instructions are the _simplest instructions_ to test stuff like the I/O, Registers and others.  The Format 2 instructions are the instructions that have _two operands_, and mainly _revolve around the registers_.  The Format 3/4 instructions are the instructions that have _one operand, and mainly revolve around the memory_.

* <u>Understanding of the SIC/XE addressing modes</u>:
    - The SIC/XE addressing modes are divided into five categories: 
        1. <u>Immediate addressing mode</u>
            - This mode is used to specify the value of the operand directly
        2. <u>Direct addressing mode</u>
            -  This mode is used to specify the address of the operand directly
        3. <u>Indirect addressing mode</u>
            - This mode is used to specify the address of the operand indirectly, where the address of the operand is obtained from operand's data.
        4. <u>Indexed addressing mode</u>
            - This mode is used to specify the address of the operand indirectly with an index register. 
        5. <u>Base-relative addressing mode</u>.  
            - This mode is used to specify the address of the operand indirectly with a base register.

* <u>Understanding of the SIC/XE directives</u>:
    - The SIC/XE directives are used to specify the attributes of the program. 
    These are divided into two categories: 
        1. <u>Assembler directives</u>
            - The Assembler directives are used to specify the attributes of the program that are used by the assembler. 
            Examples of such directives are:  `START, END, BYTE, WORD, RESB, and RESW`.
        2. <u>Machine directives</u>  
            - The Machine directives are used to specify the attributes of the program that are used by the machine.
            Examples of such directives are: `LDA, STA, ADD, SUB, MUL, DIV, and COMP`.

* <u>Understanding of Control Sections</u>:
    - Control Sections **allow for the separation of the program** into _multiple sections_ that embrace the **readability** and **maintainability** of the program. 
    - The **Linker**, at link-time, is responsible to _link the multiple sections_ of the program into a single executable program.

* <u>Understanding the Type of Symbols</u>:
    - The symbols in the SIC/XE architecture are divided into two categories: 
        1. **External Symbols**
            - The External Symbols are the symbols that are _defined in one Control Section_ but are _referred to in another Control Section_.  The Linker is responsible to resolve the External Symbols at link-time.
        2. **Absolute Symbols**
            - The Absolute Symbols are the symbols that are _defined in one Control Section_ and are _referred to in the same Control Section_.  The Assembler is responsible to resolve the Absolute Symbols.

# Objectives <a name="objectives"></a>
* The primary objective of this project is to _implement an efficient Two-Pass assembler_ for the SIC/XE architecture, in the aim of which the following sub-objectives are set:
    * To **optimize memory usage and execution time**, making the assembler suitable for use in environments with limited resources.
    * To ensure **compatibility with the SIC/XE instruction set**, enabling the assembler to accurately translate mnemonic instructions into machine code.
    * To support **all addressing modes** provided by the SIC/XE architecture, enhancing the flexibility and capability of the assembler.
    * To implement **error detection and reporting mechanisms**, assisting developers in identifying and correcting syntax or semantic errors in their assembly code.
    * To provide **clear and comprehensive documentation** on how to use the assembler, including examples of assembly programs and explanations of the assembly process.
    * To use **modern C++ features** to write _clean, efficient, and maintainable code_, following _best practices_ and _design principles_, and to have detailed discussions on the choice of Data Structures.
    * To create a **user-friendly interface** for the assembler on the _command-line interface_, to facilitate ease of use for both beginners and experienced users.
    * To ensure **extensibility** of the assembler, allowing for future enhancements such as support for additional features such as _Program Blocks_, _Parallel Processing_, and _Optimization Techniques_.
    * To structure the **project across multiple header and C++ source files**, promoting _modularity_ and _maintainability_, facilitating _parallel development_, _simplifying debugging_, and enhancing the _readability_ and organization of the codebase.
    * To employ **GNU Make** as the **build system** to _manage the compilation process efficiently_, leveraging its capabilities to automate the build process and ensure a smooth development workflow across multiple platforms.


# Design <a name="design"></a>
* The design of the Two-Pass assembler is divided into two distinct steps: 
    1. <u>The 1st pass</u>
        - This is used to _generate the Symbol Table_ respective to **each Control Section**.  The symbol table is used to store the symbols and their respective addresses to help in the second pass.
    2. <u>The 2nd pass</u>
        - This is used to _generate the Object Code_ that is **further utilised by the Linker and Loader** to process the program.  The object code is generated by using the symbol table that was generated in the first pass.

## Possible Data Structures that can be used for the Two-Pass Assembler:

###  For the First Pass:

1. **For the Symbol Table**  
As the Symbol Table is used to store the symbols (variable names) and their respective addresses in the input ASM file, and requires the _below operations_:
    - Insert a symbol and its address
    - Search for a symbol and its address
    - Delete a symbol and its address  
    We can use the following data structures:
#### a) Hash Table
A [Hash Table](https://en.wikipedia.org/wiki/Hash_table) is used to store the symbols and their respective addresses in an efficient manner.  It uses a hash function to map the symbols to their respective addresses.  The hash function tries to ensure the best that the symbols are stored in a unique location in the hash table.  The hash table ensures that the symbols can be searched, inserted, and deleted in constant time.
Provided a _good hash function_ [^1], the **time complexity** of insertion, deletion, and search operations is **O(1)** on _average_.  It is _on average_ because there can be collisions in the hash table, which can increase the time complexity to **O(n)** in the worst case.
The **space complexity** of the hash table is **O(n)** where `n` is the number of symbols in the program.
```cpp
Time Complexity: O(1) for insertion, deletion, and search operations on average.
Space Complexity: O(n) where n is the number of symbols in the program.
```
#### b) Self-Balancing Binary Search Trees
If it is very crucial to us that we have a strict upper bound on the time complexity of the operations [^8], we can opt the Symbol Table to be made of [Self-Balancing Binary Search Trees](https://en.wikipedia.org/wiki/Self-balancing_binary_search_tree) like the _AVL Tree_ [^2] or the _Red-Black Tree_ [^3].  These trees ensure that the **height of the tree is balanced**, which ensures a constant time complexity for insertion, deletion, and search operations.
```cpp
Time Complexity: O(log n) for insertion, deletion, and search operations.
Space Complexity: O(n) where n is the number of symbols in the program.
```
#### c) Trie
A [Trie](https://en.wikipedia.org/wiki/Trie) is a tree-like data structure that is used to store a dynamic set of strings.  The Trie here can be used to store the symbols and their respective addresses in an efficient manner.  The Trie ensures that the symbols can be searched, inserted, and deleted in constant time with respect to the length of the symbol.
```cpp
Time Complexity: O(L) for insertion, deletion, and search operations where `L` is the length of the symbol.
Space Complexity: O(L*n) where n is the number of symbols in the program.
```
#### d) Skip List
A [Skip List](https://en.wikipedia.org/wiki/Skip_list) is a probabilistic data structure that enables fast search, insertion, and deletion operations. By maintaining multiple layers of forward pointers, Skip Lists allow operations to skip over large sections of the list, achieving average `O(log n)` time complexity for these operations. This makes Skip Lists an efficient and practical choice for symbol tables where logarithmic operation time is desirable.
```cpp
Time Complexity: Average O(log n) for insertion, deletion, and search operations.
Space Complexity: O(n), with higher constants due to additional pointers.
```
Implementations of the above data structures can be found in the `data_structures` directory.
    1. [Hash Table](/data_structures/hash_table.hpp)
    2. [AVL Tree](/data_structures/avl_tree.hpp)
    3. [Red-Black Tree](/data_structures/red_black_tree.hpp)
    4. [Trie](/data_structures/trie.hpp)
    5. [Skip List](/data_structures/skip_list.hpp)

> Due to the _presence of **Control Sections**_, we maintain a _separate Symbol Table for each Control Section_.  This ensures that the symbols are _unique_ within the Control Section and are _not duplicated_ across Control Section. This helps to cut-off the _ambiguity_ that can take place during **the linking process**.

2) **For the Opcode Table and Register Table**  
Both, the Opcode Table and the Register Table are **Static Data Structures** that are used to store the _opcodes_ and their _respective machine codes_, and the _registers_ and their _respective machine codes_ respectively, dictated by the **hardware of the SIC/XE architecture**.  
The Opcode Table is used to validate the instructions and their respective opcodes, and the Register Table is used to validate the registers and their respective machine codes.
    - In Pass 1 to _validate the instructions_ and _their respective opcodes_, reserving proper space for the instructions in the Intermediate File.
    - In Pass 1 to _validate the Opcode_ if it is even supported by the SIC/XE architecture hardware.
    - In Pass 2 to _generate the Object Code_ by _using the Opcode Table_.

We can use the exact same data structures as mentioned above for the Symbol Table. 
Additionally, due to the virtue of them being a static data structures, we can opt for a **Hash Table**, as we _only perform search operations_ on these tables during the first and second pass.  We can meticulously design the hash function to ensure that the opcodes & register names are stored in a unique location respectively, in the hash table, yielding the worst case time complexity of **O(1)** for search operations.
```cpp
Time Complexity: O(1) (Omega) for search operations on average.
    Space Complexity: O(n) where n is the number of opcodes in the SIC architecture.
```
3) **For the Intermediate File**  
The Intermediate File is used to store the intermediate results of the first pass.  The intermediate file is used to store the Control Section, the Symbol Table, and the Program.  The intermediate file is used to generate the Object Code in the second pass.
We can use the following data structures:
#### a. Secondary Memory
- The Intermediate File can be stored in the _secondary memory_ like the hard disk.  It can further be read from the secondary memory in the second pass to generate the Object Code.
```cpp
Time Complexity: Dependent on the size of the data, Hardware of the Secondary Storage, underlying Operating System and system performance, not strictly O(1).
Space Complexity: O(n) where n is the size of the Intermediate File.
```
#### b. In-Memory Data Structures
- The Intermediate File can be stored in the memory.  It can be stored in the memory in the form of a data structure like a vector or a list, and can be read from the memory in the second pass to generate the Object Code.
```cpp
Time Complexity: 
    O(1) for insertion and search operations.
    O(n) for deletion operations.
Space Complexity: 
    O(n) where n is the size of the Object Code.
```

### For the Second Pass:
Let me set a bit of context that helps explain the _Header Record_, _Text Records_, _Modification Records_ and _End Record_ that are generated in the second pass.
The SIC/XE architecture has a _fixed-length instruction format_ that is used to store the instructions.  The fixed-length instruction format is used to store the instructions in the memory.  This is _analogous_ to the **ELF format** in the **Linux Operating System**, albeit this is a _way simpler version_ of the ELF format.
<br>
The fixed-length instruction format is used to store the instructions in the memory in the form of:
- <u>Header Record (H)</u>
    - The Header Record is used to store the _starting address_ of the program, the _name_ of the program, and the _length_ of the program.
- <u>Text Records (T)</u>
    - The Text Records are used to _store the instructions_ of the program in the form of the fixed-length instruction format.  SIC/XE likes to have its instructions in a _fixed-length_ format of _30 bytes_.
- <u>Modification Records (M)</u>
    - The Modification Records are used to store the modifications to the instructions.  For example, if one executable is _referring a symbol from another executable_, the Modification Records are used to store the modifications the instructions need to undergo to _refer the symbol from the other executable_ by the Linker.
- <u>End Record (E) </u>
    - The End Record marks the end of the program, and specifies the address of the first executable instruction.

1. **For the Object Code**
The Object Code is used to store the instructions of the program in the form of the fixed-length instruction format.  The Object Code is used by the Linker and Loader to process the program.  The Object Code is generated by using the Symbol Table that was generated in the first pass.
We can use the following data structures:
#### a. Secondary Memory
The Object Code can be stored in the secondary memory like the hard disk.  The Object Code can be stored in a file in the secondary memory.  The Object Code can be read from the secondary memory by the Linker and Loader to process the program.
```cpp
Time Complexity: Dependent on the size of the data, Hardware of the Secondary Storage, underlying Operating System and system performance, not strictly O(1).
Space Complexity: O(n) where n is the size of the Object Code.
```
#### b. In-Memory Data Structures
- The Object Code can be stored in the memory.  The Object Code can be stored in the memory in the form of a data structure like a vector or a list.  The Object Code can be read from the memory by the Linker and Loader to process the program.

```cpp
Time Complexity: 
    O(1) for insertion and search operations.
    O(n) for deletion operations.

Space Complexity: 
    O(n) where n is the size of the Object Code.
```
---
# Business Cases for Enhanced SIC/XE Assembler Design <a name="business-cases"></a>
During the development of my Two-Pass SIC/XE assembler, I identified key areas where strategic data structure selection and optimization deliver significant business value. These improvements translate directly to tangible benefits for organizations employing assembly language programming:

#### <u>Business Case 1</u>: High Performant Compute Through Optimized Symbol Management
##### Problem: 
Traditional symbol table implementations (with Linear Search) become bottlenecks in large projects due to linear search times.
##### My Solution: 
I utilize a Hash Table for Symbol Tables across multiple Control Sections. Hash tables excel at providing near-constant-time complexity for search, insertion, and deletion operations, regardless of the symbol table's size, provided a good hash function is used, which I utilise in my implementation by having a Prime Number as the size of the Hash Table [^4].
##### Business Impact: 
Faster compilation cycles translate to increased developer productivity, shorter development timelines, and ultimately, faster time-to-market for software products along with efficient memory utilization.

#### <u>Business Case 2</u>: Performance Gains Through In-Memory Processing
##### Problem: 
Storing intermediate files and the Symbol Table on secondary storage introduces latency from frequent disk I/O operations.
##### My Solution: 
Transitioning to in-memory data structures for these components, allows us to leverage the _speed of RAM_ to minimize data access times.  The intermediate file can be stored in the memory in the form of a data structure like a `std::vector` or a `std::list` (preferably `std::vector` since the hardware caches memory pages).  
The intermediate file can be _read from the memory_ in the second pass to generate the Object Code.
##### Business Impact: 
This results in significantly faster assembly times [^5], crucial for time-sensitive compilation processes, large projects, and environments where rapid prototyping is essential.

#### <u>Business Case 3</u>: Resource Optimization via Memory Usage Analysis
##### Problem: 
Inefficient data representation or storage can lead to excessive memory consumption, impacting performance, especially on resource-constrained systems as mentioned in Business Case 2[^5].
##### My Solution: 
I conduct thorough analysis of memory access patterns within the Symbol Table and intermediate file handling. This helps identify opportunities for  optimized data structures, compression, and improved cache utilization (as mentioned in Business Case 2 by the utilization of `std::vector` or any contiguous memory data structure).
##### Business Impact: 
Reduced memory footprint translates to the ability to handle larger projects efficiently, potentially lower hardware requirements, and a smaller overall operational cost.

#### <u>Business Case 4</u>: Scalability for Future-Proofing Development Efforts
##### Problem:
As software projects grow in complexity, assembler performance needs to scale accordingly to avoid becoming a bottleneck.
##### My Solution: 
I prioritize data structures and file management techniques known for their scalability, ensuring the assembler remains performant and reliable even with increasingly large and complex input programs. We can utilise **parallel processing** techniques to further enhance the scalability of the assembler, which is one of the future scopes of this project, as most hardware today is multi-core.
##### Business Impact: 
This future-proofs development efforts, allowing organizations to confidently tackle ambitious projects without the assembler becoming a limiting factor as codebases expand.

#### <u>Business Case 5</u>: Streamlined Debugging Through Enhanced Error Handling
##### Problem: 
Identifying and resolving errors efficiently is critical for developer productivity, but assembly language programming can make this challenging.
##### My Solution: 
I implement robust error detection and reporting mechanisms, including features like _symbol cross-referencing_, _referencing Unknown Instructions_ and _early error flagging_ just to name a few. This ensures that developers can quickly identify and address issues, reducing debugging time and enhancing code quality.
##### Business Impact: 
This **minimizes the time spent on debugging**, reduces the likelihood of subtle errors going unnoticed, and contributes to higher overall code quality.

#### <u>Business Case 6</u>: Enhanced Code Quality Through Error Handling during both the passes, visible in the Intermediate and Listing Files
##### Problem: 
Traditional assemblers often provide limited visibility into the assembly process, making it difficult to understand how source code translates to machine code. This lack of transparency makes debugging a frustrating and time-consuming process.
##### My Solution: 
My assembler addresses this by generating two key files:  

<u>Intermediate Files</u>: These provide a step-by-step breakdown of the assembly process, making it easy to trace how the assembler interprets and translates your code.  

<u>Listing Files</u>: These combine your source code with generated machine code, symbol tables, and clear error messages mapped to specific lines. This integrated view simplifies debugging and enhances code understanding.

##### Business Impact: 
This transparency promotes faster debugging, reduces errors, and improves code quality through enhanced readability and understanding of the assembly process. Ultimately, this translates to quicker development cycles and more reliable software products.


#### <u>Business Case 7</u>: Accelerating Development with Makefile-Driven Builds
##### Problem: 
Building projects in C++ often involves multiple steps: _assembling_ multiple source files, _linking_ object files, potentially running `pre` or `post-processing` scripts. Manually managing these steps is error-prone, time-consuming, and difficult to reproduce consistently, especially across different development environments.
And given that I have divided the project into multiple files, it is essential to have a build system that can compile the project efficiently, abstracting the complexity of the build process.
#### My Solution: 
I've leveraged GNU Makefiles [^6], a powerful build automation tool, to manage the entire project build process. Here's how Makefiles bring value:
- _Automation_: Makefiles define all build steps and their dependencies. A single command ("make") executes the entire build process correctly and efficiently.
- _Dependency Tracking_: Makefiles automatically determine which files need to be recompiled or relinked based on their dependencies. This prevents unnecessary rebuilds, saving significant time during development.
- _Reproducibility_: Makefiles ensure that the project can be built consistently across different machines and environments using the same defined steps, reducing the "it works on my machine" problem.
- _Flexibility_: Makefiles allow for customization of build targets, allowing developers to easily perform specific actions like running tests or generating documentation.
#### Business Impact:
- _Increased Developer Productivity_: 
Automation eliminates the tedious manual build steps (shooting yourself in the foot especically with C++), freeing developers to focus on writing and testing code.
- _Faster Development Cycles_: 
Automated dependency tracking and parallel builds (possible with `make -j`) significantly reduce build times, especially for large projects.
- _Reduced Errors_: Makefiles enforce a consistent and reliable build process, minimizing the potential for human error and ensuring consistent output.
- _Improved Code Maintainability_: 
Makefiles act as clear and concise documentation of the build process, making it easier for others to understand and maintain the project over time.

By meticulously addressing these business cases and the design choices, my SIC/XE assembler will **not only be functionally sound** but showcases a **robust design** that aligns with _industry best practices_ and _modern development standards_. This approach ensures that the assembler is not just a standalone tool but a strategic asset that accelerates development, enhances code quality, and future-proofs development efforts.

---
# A sample execution of the Two-Pass Assembler with figures <a name="sample-execution"></a>

1. <u>Input File</u>  
    ![Input File](/figs/sample_input.png)  
    The above input consists of 3 Control Sections: `Main`, `RDREC`, and `WRREC`.  Each Control Section has a set of instructions and directives.

    When this is fed as input to the Two-Pass Assembler, the following happens:
    1. The First Pass generates the Symbol Table, and an Intermediate file for Layout of the program in the memory along with errors (if any), for each Control Section.
    2. The Second Pass generates the Object Code for each Control Section and a Listing File depicting detailed information about the program, and errors if any.

2. <u>Intermediate File</u>  
    ![Intermediate File](/figs/sample_intermediate.png)  
    The above intermediate file consists of the Control Section, the Symbol Table, and the Program.  The intermediate file is used to generate the Object Code in the second pass.

3. <u>Object Code</u>  
    ![Object Code](/figs/sample_object.png)  
    The above object code consists of the Header Record, Text Records, Modification Records, and End Record.  The object code is used by the Linker and Loader to process the program.

4. <u>Listing File</u>  
    ![Listing File](/figs/sample_listing.png)  
    The above listing file consists of the detailed information about the program.  The listing file is used to debug the program and to understand the program.

---
# Usage  <a name="usage"></a>

- The source code for the Two-Pass Assembler is available in the **[src](/src)** directory.
- The Design and Implementation of the Two-Pass Assembler for the SIC/XE architecture is done in _C++_, adhering to its Modern Standards.  The code is _structured across multiple header and C++ source files_, promoting **modularity** and **maintainability**. The code is _well-commented and structured_, making it easy to understand and follow. 
- It is built using the **GNU Make build system**, which _automates the compilation process_ and ensures a smooth development workflow across multiple platforms.  The **[Makefile](/src/Makefile)** defines all build steps and their dependencies, allowing developers to compile the project efficiently with a single command.  
Below is a diagram depicting the structure of the project:

![Project Structure](/figs/Sys_Design.png)

The following steps will guide you on how to build and run the Two-Pass Assembler on your local machine:
1. Clone the repository
    ```bash
    git clone https://github.com/ShriHari33/sic_xe-assembler.git
    cd sic_xe-assembler/src
    ```

2. Build the program
    ```bash
    make
    ```
    This will compile, link, and build the program.  The executable `assembler` will be generated in the `src` directory.

    <br>    

3. Run the program
    ```bash
    ./assembler {input_file_name}
    ```
    - Replace `{input_file}` with the path to the input file you want to assemble.  A sample input file is provided in the `src` directory, with the name `input.txt`. So to test that, type `./assembler input.txt`.

    <br>

    This will generate the _Intermediate File_, _Object Code_, and _Listing File_ in the `src` directory.  The _command line_ will display the errors if any.  If there are _any errors_ in the First Pass, the Second Pass will not be executed and the user will be prompted to correct the errors in the `Intermediate File`. 
    
    <br>

4. Clean the program
    ```bash
    make clean
    ```

    In case you want to clean the program, you can run the above command.  This will _remove_ the _object files_ and the _executable file_.

---
# Future Scope <a name="future-scope"></a>
1. **Parallel Processing**:  
    - The Two-Pass Assembler can be enhanced to support _parallel processing_ to _speed up the assembly process_.  This can be achieved by _splitting the assembly process_ into multiple threads, each handling a separate Control Section.  I reckon this will significantly _reduce the overall assembly time_ and _improve the performance_ of the assembler.
2. **Optimization Techniques**:  
    - The Two-Pass Assembler can be further optimized by implementing _optimization techniques_ such as _dead code elimination_, _constant folding_, and _loop optimization_.  Taking the idea of LLVM's MLIR [^9] optimizations, we can _transform the intermediate representation_ of the program to _optimize the code_ and _reduce the execution time_.
3. **Program Blocks**:
    - In addition to the already present Control Sections, this Two-Pass Assembler can be extended to support _Program Blocks_, which allow the _separation of the program into multiple blocks_.  This will further enhance the _readability_ and _maintainability_ within a _single program_ and _facilitate the reuse of code_.
4. **Macro Processing**:
    - The Two-Pass Assembler can be enhanced to support _macro processing_, which allows the _definition and expansion of macros_ in the assembly code.  This will _reduce code duplication_ and _improve code readability_. This part is the most exciting part for the future scope of this project for me!
    > Please submit PRs if you have any ideas or suggestions for the future scope of this project.  I would be more than happy to share, discuss, and implement them with the community!

---
# About Me <a name="about-me"></a>
As a pre-final year Computer Science and Engineering student at KLE Technological University, I, Shrihari, have developed a strong foundation in low-level systems, with a particular focus on **Linux device drivers**, **Interposing Libraries** and **ELF file systems**. My proficiency in C and Modern C++ has been instrumental in my work with these systems.

My professional experience includes _contributing to projects_ at **NVIDIA**, which has solidified my aspiration to _pursue a career in **Systems Engineering**_. This exposure has not only enhanced my technical skills but also made me _realise my passion_ for working with low-level software.

Beyond my technical pursuits, I maintain a keen _interest in Mathematics_, finding intellectual stimulation in its principles and applications. Additionally, I derive great enjoyment from **operating manual transmission vehicles**, _appreciating the mechanical intricacies and control_ they offer. I also love to _tinker_ with **hardware components!**

These diverse interests and experiences have shaped my _academic and professional trajectory_, positioning me well for future endeavors in the field of **Systems Engineering**.  I am currently preparing for **GATE CSE 2025** for my Masters to explore various other Systems Engineering domains such as **Computer Graphics**, **GPU Programming**, **HPC** and **Advanced Computer Architecture**. 

---

# References <a name="references"></a>
[^1]: Wikipedia, "Hash function," Available: https://en.wikipedia.org/wiki/Hash_function.
[^2]: Wikipedia, "AVL tree," Available: https://en.wikipedia.org/wiki/AVL_tree.
[^3]: Wikipedia, "Red–black tree," Available: https://en.wikipedia.org/wiki/Red%E2%80%93black_tree.
[^4]: Stack Overflow, "Why should hash functions use a prime number modulus?" Available: https://stackoverflow.com/questions/1145217/why-should-hash-functions-use-a-prime-number-modulus.
[^5]: Harvard University, CS61, "Storage," 2019, Available: https://cs61.seas.harvard.edu/site/2019/Storage/.
[^6]: GNU, "GNU Make," Available: https://www.gnu.org/software/make/.
[^7]: L. L. Beck, "System Software: An Introduction to Systems Programming," 3rd ed., Pearson Education, 1996, Available: https://www.amazon.in/System-Software-Introduction-Systems-Programming/dp/0201423006.
[^8]: D. Salomon, "Assemblers and Loaders," Available: https://www.davidsalomon.name/assem.advertis/asl.pdf.
[^9]: LLVM, "MLIR: Multi-Level Intermediate Representation," Available: https://mlir.llvm.org/.