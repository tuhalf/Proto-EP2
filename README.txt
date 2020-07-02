# Proto - Educational Pupose 8-Bit Processor
Made for students, teachers, and any soul who wants to understand how a CPU works. Open Source.

Instruction Sets
 I : Instruction 
{ }: Register 
[ ]: Adress
( ): Condition - Based on registers A and B, takes parameters > or =
______________________________________________________________________
ADD [X]
	Add two numbers that are in A and B registers and write it to X address

SUB [X]- Subtract
	Subtract A from B and write it to X address 

LOAD {X} 
	Load data from next adress to X register 

STORE [X]
	Store data form register A to X adress in RAM 

LWJ {X} [Y] - Load With Jump
	Load data from Y adress to X register

JUMP [X]
	Jump to X adress in RAM

CJ (X) [Y] - Conditional Jump (Carry or Zero)
	If X condition is TRUE, jump to Y adress in RAM
  
LSCR [X] [Y]
	Loads the data stored in X address from RAM to Y address from GPU (Each bit is one pixel)
  
LSCR {X} [Y]
	Loads the data stored in X register to Y address from GPU (Each bit is one pixel)
  
LPNC \[X]
	Starts loading the punch card starting from X address
  
HLT
	Halts the system
	
	
Microcode Explained
bits
0nnnn00000nn00__
1000000000000000    HLT - 1
0100000000000000    REG_READ - 2
0010000000000000    AWRITE - 3	(Connected to ALUWRITE and Or'ed with AWRITE because there are not enough controll bits)
0001000000000000    FL - 4
0000100000000000    ALUWRITE - 5(Connected to RAM_WRITE and Or'ed with AWRITE because there are not enough controll bits)
0000010000000000    ALUSUB - 6
0000001000000000    PC_LE - 7
0000000100000000    PC_LRS - 8
0000000010000000    RAM_LRS - 9
0000000001000000    SLC_ADDR - 10
0000000000100000    RAM_OUT - 11
0000000000010000    INSTREAD - 12
0000000000001000    COUNT - 13
0000000000000100    RAM_AS - 14
0000000000000010    --
0000000000000001    --
01111000 00110000				00------000
----JUMP [X]---- (N-------,XXXXXXXX,XXXXXXXX)	nn100000                        
COUNT						00100000000 - 01111000 00111000
PC_LRS || RAM_OUT				00100000100 - 01111001 00010000
COUNT						00100000010 - 01111000 00111000	
RAM_OUT || PC_LE			        00100000110 - 01111010 00010000
RAM_OUT || INSTREAD				00100000001 - 01111000 00000000

-----------------------------------------------------------------------------------

----LOAD {X}---- (X-------)			                nn010000    
COUNT                                           00010000000 - 01111000 00111000
RAM_OUT || REG_READ                             00010000100 - 00111000 00010000
COUNT                                           00010000010 - 01111000 00111000
RAM_OUT || INSTREAD                             00010000110 - 01111000 00000000

----------------
----ADD [X]---- (N-------,XXXXXXXX,XXXXXXXX)    nn110000
COUNT || RAM_OUT || RAM_LRS || SLC_ADDR         00110000000 - 01111000 11011000
COUNT || RAM_OUT || SLC_ADDR                    00110000100 - 01111000 01011000
RAM_AS || ALUWRITE                              00110000010 - 01110000 00110100
RAM_OUT || INSTREAD                             00110000110 - 01111000 00000000

----------------
----SUB [X]---- (N-------,XXXXXXXX,XXXXXXXX)    nn001000
COUNT || RAM_OUT || RAM_LRS || SLC_ADDR         00001000000 - 01111000 11011000
COUNT || RAM_OUT || SLC_ADDR                    00001000100 - 01111000 01011000
RAM_AS || ALUSUB || ALUWRITE                    00001000010 - 01110100 00110100
RAM_OUT || INSTREAD                             00001000110 - 01111000 00000000

----------------
----STORE [X]---- (N-------,XXXXXXXX,XXXXXXXX)  nn101000
COUNT || RAM_OUT || RAM_LRS || SLC_ADDR         00101000000 - 01111000 11011000
COUNT || RAM_OUT || SLC_ADDR                    00101000100 - 01111000 01011000
RAM_AS || AWRITE                                00101000010 - 01011000 00110100
RAM_OUT || INSTREAD                             00101000110 - 01111000 00000000

-----------------
----LWJ {X} [Y]---- (X-------,YYYYYYYY,YYYYYYYY)nn111000
COUNT || RAM_OUT || RAM_LRS || SLC_ADDR         00111000000 - 01111000 11011000
COUNT || RAM_OUT || SLC_ADDR                    00111000100 - 01111000 01011000
RAM_AS || RAM_OUT || REG_READ                   00111000010 - 00111000 00010100
RAM_OUT || INSTREAD                             00111000110 - 01111000 00000000

-------------------
----CJ (ZERO) [Y]-- (N-1-----,XXXXXXXX,XXXXXXXX)nn001100
COUNT || FL                                     00001100000 - 01101000 00111000
01OOOOOO{PC_LRS || RAM_OUT                      01001100100 - 01111001 00010000
COUNT || RAM_OUT || PC_LE                       01001100010 - 01111010 00011000
RAM_OUT || INSTREAD}                            01001100110 - 01111000 00000000
COUNT                                           00001100100 - 01111000 00111000
COUNT || RAM_OUT || INSTREAD                    00001100010 - 01111000 00001000

---------------------
---CJ (CARRY) [Y]-- (N-1-----,XXXXXXXX,XXXXXXXX)nn100100
COUNT || FL                                     00100100000 - 01101000 00111000
10OOOOOO{PC_LRS || RAM_OUT                      10100100100 - 01111001 00010000
COUNT || RAM_OUT || PC_LE                       10100100010 - 01111010 00011000
RAM_OUT || INSTREAD}                            10100100110 - 01111000 00000000
COUNT                                           00100100100 - 01111000 00111000
COUNT || RAM_OUT || INSTREAD                    00100100010 - 01111000 00001000

---------------------
----HLT----                                     nn111111
HLT                                             00111111000 - 11111000 00110000

-----------















