# DECA-lab2-challenge
This is a ISSIE solution to 16bits multiplied by 16bits multiplier within 64 full adders. It can be runned within 8 clock cycles and it is an extra sheet inserted to lab 2 EEP1 design.

### MULT sheet
A very important part is the clock cycle. From cycle 0, the NUMA and NUMB are loaded into the registers for later uses. After this, the NUMA is proceeded from LSB to the MSB from cycle 0 to 7, every 3 digits. Moreover, the content in the register at cycle 7 is cleared and the output is written to the register files. 

<img width="824" height="514" alt="image" src="https://github.com/user-attachments/assets/4db86cd0-1777-438d-bc71-4cec24dbffef" />

A simulated result:
<img width="944" height="267" alt="image" src="https://github.com/user-attachments/assets/4635a637-cff4-4deb-8589-c433d7f81af2" />



### Algorithm

<img width="782" height="34" alt="image" src="https://github.com/user-attachments/assets/ec711cd7-d1da-45bb-bd6f-5ed681b2df55" />


The algorithm we used is radix4 multiplication. NUMA is split into 8 indices, starting from (1:-1) to (15:13), which is turned into 8 opcodes from cycle 0 to cycle 7. The opcode is calculated through Radix4 sheet, and it is a three bit signed number.

**Radix4 sheet**


<img width="997" height="457" alt="image" src="https://github.com/user-attachments/assets/933a5fa5-27e5-48ee-a40a-5dcd0bf9a7ab" />

**Mathematical prood for the opcode and the corresponding weight**

<img width="937" height="538" alt="image" src="https://github.com/user-attachments/assets/4368edfe-1ccf-4250-a60a-aa5e7ac673c3" />


**MULT B sheet**

<img width="806" height="529" alt="image" src="https://github.com/user-attachments/assets/09f45f87-51bf-4d9c-becb-8057f010a956" />
In the MULTB sheet, we take the absolute value of the opcode and the sign of the opcode. For example, 6 in MUX1 is 0b110, which is -2. The sign value(ADDIN) is 1 and the magnitude is 2. The output of the MULT B sheet is a 32 digit number is 

$$\mathrm{NUMB} \times \left| \mathrm{opcode} \right|$$


**Shift operation**

<img width="794" height="564" alt="image" src="https://github.com/user-attachments/assets/24ed422b-34f8-4b23-80dd-8da6f7be186c" />

We shifted the eight indices with the corresponding weight from cycle 0 to cycle7.
$$Each part = \mathrm{NUMB} \times \left| \mathrm{opcode} \right| \times 2^{2n}$$
where n is the number of clock cycle.

**32-bit ADDSUB**

<img width="365" height="355" alt="image" src="https://github.com/user-attachments/assets/54d829e6-b51d-4504-a720-669339ca6758" />

If ADDIN from MULT B sheet is one, we subtract the previous result we calculated from the shift operation block. Otherwise, we use the register to accumulate the eight(OPCODE multiplied by NUMB) indices. This ADDSUB module gives out the final result at the clock cycle 7. Therefore, the whole module takes 8 cycles to calculate the result.

### Writing back through MOVC1
<img width="560" height="282" alt="image" src="https://github.com/user-attachments/assets/afb86f14-de56-4409-9966-2ad1c36ee6c5" />
We used MOVC1 instruction to do the operation R0 := R0 \times R1. Therefore, we need an EN at clock cycle 3(in the example) to let the counter in the MULT sheet to start counting from 0 and start run the multiplication. By repeating the command 8 times, the calculated answer is at R0.
<img width="920" height="275" alt="image" src="https://github.com/user-attachments/assets/8f3e9ac7-e12e-4658-8f4d-8e1af7dd7a73" />

**ALU sheet**
<img width="972" height="155" alt="image" src="https://github.com/user-attachments/assets/0fc24478-534a-4dae-8605-41043d181b6d" />

Then how to link it to the defined instruction MOVC1? The only difference between MOV and MOVC1 is INS(4:2) is 1(field C) for MOVC1, and (4:2) is 0 for MOV if values are read from REGA and REGB, MOV uses IMMS8, then OP2SEL will be 1. Therefore, in order to let the value output be MOVC1 instead of MOV, we used MUX4 and let the logic be ~OP2SEL && (IMM(4:2) == 0b1) in order to write the output result of MULT sheet, and enables EN only at the first cycle of MULT, which is the same condition for MUX4 to write the MOVC1 value.
<img width="644" height="538" alt="image" src="https://github.com/user-attachments/assets/7f763b58-d173-48fa-8170-86b4a270b085" />

