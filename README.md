# 32 Bit RISC based MIPS Processor

## MIPS32  
- 32 x 32 bit GPRs [R0 to R31]  
- R0 hardwired to logic0  
- 32 bit Program Counter (PC)  
- No flag registers (carry, zero, sign..etc)  
- Few Addresing Modes  
- Only Load and Store instructions can access memory  
- We assume memory word size is 32 bits (word addressable)
  
## Addressing Modes  
| Addressing Mode | Example Instruction |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Register addressing | ADD R1,R2,R3      |
| Immediate addressing | ADDI R1,R2, 200       |
| Base addressing      | LW R5, 150(R7)    |
| PC relative addressing  | BEQZ R3, Label   |
| Pseudo-direct addressing | J Label      |
## ▫️ Instructions Considered  
Not all instructions of MIPS32 are considered in this design, for implementation sake only a few instructions are considered, mentioned below:  
- Load and Store Instructions  
```
LW R2,124(R8) // R2 = Mem[R8+124]  
SW R5,-10(R25) // Mem[R25-10] = R5  
```
- Arithmetic and Logic Instructions (only register operands)  
```
ADD R1,R2,R3 // R1 = R2 + R3  
ADD R1,R2,R0 // R1 = R2 + 0  
SUB R12,R10,R8 // R12 = R10 – R8  
AND R20,R1,R5 // R20 = R1 & R5  
OR R11,R5,R6 // R11 = R5 | R6  
MUL R5,R6,R7 // R5 = R6 * R7  
SLT R5,R11,R12 // If R11 < R12, R5=1; else R5=0 
```
- Arithmetic and Logic Instructions (immediate operand)  
```
ADDI R1,R2,25 // R1 = R2 + 25  
SUBI R5,R1,150 // R5 = R1 – 150  
SLTI R2,R10,10 // If R10<10, R2=1; else R2=0 
```
- Branch Instructions  
```
BEQZ R1,Loop // Branch to Loop if R1=0  
BNEQZ R5,Label // Branch to Label if R5!=0  
```
- Jump Instruction  
```
J Loop // Branch to Loop unconditionally  
```
- Miscellaneous Instructioon  
```
HLT // Halt execution 
```
## Instruction Encoding  
![instruction encoding](https://github.com/user-attachments/assets/7cf3db4e-421e-4c03-b74a-6327d3b0537a)

- shamt : shift amount, funct : opcode extension for additional functions.
- Some instructions require two register operands rs & rt as input, while some require only rs. 
- This requirement is only identified only after the instruction is decoded. 
- While decoding is going on, we can prefetch the registers in parallel, which may or may not be used later. 
- Similarly, the 16-bit and 26-bit immediate data are retrieved and signextended to 32-bits in case they are required later. 

## Stages of Execution  
The instruction execution cycle contains the following 5 stages in order:  
1. IF : Instruction Fetch  
2. ID : Instruction Decode / Register Fetch  
3. EX : Execution / Effective Address Calculation  
4. MEM : Memory Access / Branch Completion  
5. WB : Register Write-back  
- micro operations not shown here.
  
## Non Pipelined DataPath  
![non pipelined data path](https://github.com/user-attachments/assets/18e5afa2-491b-4d4e-ba83-bcbc56fd1f2e)

## Pipelined DataPath 
<img src="https://github.com/user-attachments/assets/ccbed9f0-b6f6-487a-ab5a-cb6266f64384" width="200" />

## Example Program Testbench Code  
Steps:  
1. Initialize register R1 with 10.  
2. Initialize register R2 with 20.  
3. Initialize register R3 with 25.  
4. Add the three numbers and store the sum in R5. 
 
Instructions :  
| Assembly Instruction  | Machine Code | Hexcode |  
| ------------- | ------------- | ------------- |  
| ADDI R1,R0,10  | 001010 00000 00001 0000000000001010  | 2801000a  |  
| ADDI R2,R0,20 | 001010 00000 00010 0000000000010100  | 28020014  |  
| ADDI R3,R0,25 | 001010 00000 00011 0000000000011001  | 28030019  |  
| OR R7,R7,R7 (dummy)| 001010 00000 00011 0000000000011001  | 0ce77800  |  
| OR R7,R7,R7 (dummy)| 001010 00000 00011 0000000000011001  | 0ce77800  |  
| ADD R4,R1,R2 | 000000 00001 00010 00100 00000 000000  | 00222000  |  
| OR R7,R7,R7 (dummy)| 001010 00000 00011 0000000000011001  | 0ce77800 |  
| ADD R5,R4,R3 | 000000 00100 00011 00101 00000 000000  | 00832800  |  
| HLT | 111111 00000 00000 00000 00000 000000  | fc000000  |  

 
Waveform :  
![output waveform](https://github.com/user-attachments/assets/f6bfb2c6-4986-4da7-817a-31fc8b622564)

 Console output :  
``` R0 -  0
R1 - 10
R2 - 20
R3 - 25
R4 - 30
R5 - 55  
```  
 
## Known problems and issues  
Following pipelining hazards are present in the given design :  
- Structural Hazards due to shared hardware.  
- Data Hazards due to instruction data dependency.  
- Control hazards due to branch instructions.  
## References  
[NPTEL \& IIT KGP 'Hardware Modeling using Verilog'- Prof. Indranil Sengupta](https://nptel.ac.in/courses/106105165)
