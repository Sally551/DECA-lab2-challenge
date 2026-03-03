# DECA-lab2-challenge
This is a ISSIE solution to 16bits multiplied by 16bits multiplier within 64 full adders. It can be runned within 8 clock cycles and it is an extra sheet inserted to lab 2 EEP1 design.

### MULT sheet
A very important part is the clock cycle. From cycle 0, the NUMA and NUMB are loaded into the registers for later uses. After this, the NUMA is proceeded from LSB to the MSB from cycle 0 to 7, every 3 digits. Moreover, the content in the register at cycle 7 is cleared and the output is written to the register files. 

<img width="993" height="609" alt="image" src="https://github.com/user-attachments/assets/64a757f8-206a-4990-aaeb-61304f897284" />

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
