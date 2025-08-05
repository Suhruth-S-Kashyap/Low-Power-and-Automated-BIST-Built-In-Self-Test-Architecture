# Low-Power-and-Automated-BIST-Built-In-Self-Test-Architecture
This project presents the design and VLSI implementation of a “low-power Built-In Self-Test (BIST) architecture” using Cadence Virtuoso. The main focus is on optimizing power consumption and automation.

## Introduction
Built-In Self-Test (BIST) is a design technique that enables a system or circuit to automatically perform diagnostic tests on itself. Instead of relying on external testing tools or human intervention, a BIST enabled system generates its own test patterns, runs tests, and evaluates the results internally. This not only reduces the cost and time required for testing but also improves the reliability of the device by enabling continuous self-checks throughout its operational life.

By combining power gating with optimized test patterns and testing, we can ensure that only the parts of the circuit that are actively involved in the test are powered on, while the idle parts are powered off or put into a low-power state. This significantly reduces power consumption during testing without compromising the accuracy of the test. Also, the test patterns are generated only in testing mode i.e., automated testing of the circuit that generates test patterns only when a fault is detected. 

o Power Gating: Think of this as turning off lights in a room you’re not using. In BIST, power gating involves shutting off power to parts of the system that aren’t actively being tested. Fine Grain Power Gating (FGPG) is the process of adding a sleep transistor to every cell that is to be turned off.

o Automated Testing: Automated testing of the circuit that generates test patterns only when a fault is detected.

## BIST Architecture
<img width="584" height="213" alt="image" src="https://github.com/user-attachments/assets/1890a922-12e4-42b0-9398-a4f02e8e01b1" />

Test Pattern Generator (TPG): Test Pattern Generator is used to generate custom and efficient test patterns for the given DUT. The test vectors generated are designed to simulate and check for faults across different parts of a circuit. We have used a 4-bit LFSR as TPG.

CUT/DUT: It is the Design/Circuit under test that is tested for having faults and locating them using the TPG and ORA. It can be a memory unit/processor or any other IC. The BIST logic is applied and made specifically for the given IC. We have used Hamming Architecture as our main DUT.

Output Response Analyzer (ORA): It analyses the outputs from the DUT and compares them against the expected outputs. If there are any discrepancies compared to the expected outputs, it gives a signal that a fault exists in the DUT circuit.

Test Controller/BIST Controller: It is used to control the normal operation and the testing operation of the circuit. It is responsible for triggering the test sequence, initiating pattern generation and analysing the outputs for faults using ORA(output response analyser). We have taken a 2:1 MUX for the BIST controller module of the circuit.

Isolation circuitry: It is used to isolate the normal operation of the DUT from the testing operation when scanning for faults in the circuit.

## BIST using Hamming Architecture as DUT
<img width="797" height="475" alt="image" src="https://github.com/user-attachments/assets/dd19833b-f469-4f30-be7c-50abe7d9884d" />

## Implementation of 2 I/P and 3 I/P NAND Gates

## Schematic of 2-input NAND gate
<img width="684" height="253" alt="image" src="https://github.com/user-attachments/assets/d9639d4c-2485-4d77-a378-c1a28ad0ce12" />

## Schematic of 3-input NAND gate
<img width="698" height="288" alt="image" src="https://github.com/user-attachments/assets/f2e027f0-a5cb-4922-bd0c-f98373cede22" />

The sleepin transistor controls the leakage currents by enabling sleep mode at idle. It cuts off the power supply when it is not needed which reduces dynamic power consumption (during active switching).

When active: The sleepin transistor is turned off to high resistance state, allowing the power gating transistor to completely isolate the block from the power supply.

When in sleep mode: The sleep-in transistor is turned on, creating a path to ground for the leakage currents, therefore reducing the leakage power. 

## D Flip-Flop using 2 I/P and 3 I/P NAND gates
<img width="752" height="254" alt="image" src="https://github.com/user-attachments/assets/4ef5f9fd-d7f9-489d-8a47-88a31fef4da6" />

## 4-bit LFSR (using DFF) as TPG
<img width="655" height="288" alt="image" src="https://github.com/user-attachments/assets/514d9d63-9acd-4db0-a6a3-c756b39da02e" />

LFSR block is used to generate random sequence according to the feedback function. The standard polynomial taken is 1+x3+x4. Generated random sequence is provided as the input to the DUT. 

## Hamming Encoder and Decoder Circuit as DUT

## Hamming Encoder (Parity Bit Generator) Schematic
<img width="753" height="342" alt="image" src="https://github.com/user-attachments/assets/94195657-8ddc-4f2c-8a67-99605701d3da" />

Three parity bits are mapped and added to the data block. In the configuration, each circle has a parity bit chosen so that the number of bits in each circle is odd. P5 = D1^D2^ D4, P6 = D2^ D3^D4,P7=D1^D3^D4.
Once the parity bits are generated, message bits along with parity bits are given to the decoder as input. During the transmission of signals from encoder to decoder in real time, there may be error in message bits due to the change in voltage levels because of various environmental factors thus, inverting the bits. We have injected a fault at D2 message bit using an inverter to check whether the decoder is able to retrieve back the original message bits or not.

## Hamming Decoder Schematic: Hamming Decoder consists of Error Bit Checker and Error Bit Corrector Circuits.

## Error Bit Checker Schematic
<img width="730" height="382" alt="image" src="https://github.com/user-attachments/assets/9cb3c980-d7ea-499d-a469-cceea83830c3" />

Error bit checker unit is used to detect error in the 7-bit word. When a received 7-bit word is transmitted, the receiver recalculates the parity bits and compares them with the received parity bits. If no errors occurred, the syndrome will be 000, which indicates that all parity checks are satisfied. If there is an error, the syndrome will be a non-zero binary value. E1 = D1^D2^D4^P5,E2=D2^D3^D4^P6,E3=D1^D3^D4^P7.

## Error Bit Corrector Schematic
<img width="704" height="381" alt="image" src="https://github.com/user-attachments/assets/7305b180-62a0-4179-8791-582e2e9a8abf" />

Error bit correction unit is used to correct the detected error. If the syndrome is 000, no error is detected. If the syndrome is non-zero, the syndrome vector indicates the position of the erroneous bit. Once the erroneous bit is identified using the syndrome, it can be corrected by flipping the bit at that position. O1=E1.NOT(E2).E3^D1, O2=E1.E2.NOT(E3)^D2, O3=NOT(E1).E2.E3^D3,O4=E1.E2.E3^D4.

## ORA  Schematic
<img width="834" height="246" alt="image" src="https://github.com/user-attachments/assets/2ebdc86f-767b-4599-b750-6053e05a8024" />

## BIST Controller (2:1 MUX) Schematic
<img width="753" height="338" alt="image" src="https://github.com/user-attachments/assets/afceb1f3-96a2-4d11-9e4f-1051847a23e7" />

## Results and Discussions

## Test Pattern Generator (LFSR) Waveform using the standard polynomial 1+x3+x4
<img width="753" height="294" alt="image" src="https://github.com/user-attachments/assets/199b3427-2e4f-4552-971a-2030fe789c8c" />

The test pattern sequence starts with 1111 and does not contain an all zero state. There are 16 different combinations from the 4- bit LFSR.

/net023 is the clock waveform. It is of 20ns period and 10ns pulse width.

q1, q2, q3 and q4 are the output from the 4-bit LFSR according to the applied polynomial.

The dynamic power of LFSR using FGPG is 1.58mW. This reduces the power consumed compared to a conventional LFSR.

## TPG waveform without any fault indicating that no test patterns are generated during normal mode as there are no faults in DUT
<img width="752" height="243" alt="image" src="https://github.com/user-attachments/assets/03444a8b-9092-425c-b170-eca81b6f9c67" />

## Normal Hamming encoding and decoding architecture waveform with O2 message bit correctly decoded after inverting D2 in the channel
<img width="757" height="202" alt="image" src="https://github.com/user-attachments/assets/18be101b-ef95-487e-a6b7-1683da2a2b13" />

## Hamming encoding and decoding architecture waveform with stuck at 1 fault injected in D1 message bit
<img width="752" height="200" alt="image" src="https://github.com/user-attachments/assets/5926ca4f-3444-42ae-b682-58f502aa6d42" />

## ORA Control_out signal with fault
<img width="745" height="278" alt="image" src="https://github.com/user-attachments/assets/ad5afd42-d0a2-46d9-a777-0e2aa9d48c8e" />

/S1 and /S2 indicate the select lines of 4:1 MUX.

/ORA_out is the ORA output after comparing faulty and correct bits.

/net020 is the control signal from the OR gate of ORA to the 2:1 MUX controlling the clock signal of TPG(LFSR).

## Power Comparison Table
<img width="1501" height="591" alt="image" src="https://github.com/user-attachments/assets/52686ad4-2d8c-42ac-bf43-516203f16ce3" />

## Conclusion
In this project, a Built-In Self-Test (BIST) architecture that is both efficient and low-power was proposed, designed to enhance the reliability and fault detection capabilities of integrated circuits (ICs) while simultaneously reducing power consumption. Our proposed architecture reduces the overall power consumption of the circuit without compromising the accuracy of the faults detected during testing. A power reduction of 8.3% can be seen when using FGPG in case of D Flip-Flop. This leads to more power reduction in the 4-bit LFSR. There is also an observed power reduction of 41.1% using FGPG in case of the Hamming BIST Architecture. We have validated the outcomes during simulation using Cadence Virtuoso using power gating technique. The proposed BIST architecture is both scalable and adaptable to various types of ICs. 

## 🔖 License
This project is licensed under the [Apache License 2.0](LICENSE) 

## 👤 Author
**Suhruth S K**  
