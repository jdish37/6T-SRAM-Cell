# Project Overview

This project involves the design and simulation of a 6T SRAM memory cell using Cadence Virtuoso. The goal is to implement the transistor-level schematic of the SRAM bit-cell, verify its read, write, and hold operations, and evaluate its stability under different operating conditions. Supporting circuits such as the pre-charge block and sense amplifier are also included to create a complete SRAM read/write environment.

The work focuses on understanding how the 6-transistor structure stores data, how bit-lines and the word-line control memory operations, and how cell stability depends on sizing, noise margins, and access transistor strength. Simulation results confirm correct functionality, disturbance-free reading, and reliable data retention.

Key aspects covered:
- 6T SRAM schematic and transistor sizing  
- Write ‘0’, Write ‘1’, Read ‘0’, Read ‘1’, and Hold simulations  
- Precharge and sense amplifier design  
- Stability analysis using Static Noise Margin (SNM)  

---

# Index

- [Project Overview](#project-overview)
- [Design of 6T SRAM Circuit Using Cadence Virtuoso](#design-of-6t-sram-circuit-using-cadence-virtuoso)
- [Pre-Charge Circuit](#pre-charge-circuit)
- [Sense Amplifier](#sense-amplifier)
- [Write '0' Operation](#write-0-operation)
- [Write '1' Operation](#write-1-operation)
- [Read '1' Operation](#read-1-operation)
- [Read '0' Operation](#read-0-operation)
- [Stability of SRAM Cell](#stability-of-sram-cell)
  - [Hold State Butterfly Analysis](#hold-state-butterfly-analysis)
  - [Read State Butterfly Analysis](#read-state-butterfly-analysis)
- [Conclusion](#conclusion)

---

# Design of 6T SRAM Circuit Using Cadence Virtuoso

The 6T SRAM cell is designed using the gpdk180 (180 nm Generic PDK) library in Cadence Virtuoso. The design process begins with creating the transistor-level schematic using gpdk180 NMOS and PMOS devices, followed by proper sizing to meet write ability, read stability, and hold robustness requirements. The bit-cell consists of two cross-coupled CMOS inverters acting as the storage element and two NMOS access transistors controlled by the Word Line (WL) to interface with the Bit Lines (BL and BLB).

Using gpdk180 ensures realistic parasitic behavior, threshold voltages, leakage characteristics, and device models suitable for academic CMOS memory design. The schematic is organized with clearly labeled nodes (Q, QB), supply rails, BL/BLB, and WL to make simulations structured and easily debuggable. After schematic completion, a dedicated testbench is created to drive the BLs, WL, and supply lines for read/write analysis.

This design is evaluated through multiple transient simulations, capturing voltage transitions, cell stability, bitline behavior, and response under switching conditions. Proper sizing ratios (pull-down > access > pull-up) are used to ensure that the SRAM meets the standard requirements for write ability and read SNM in 180 nm technology.

Key design tasks:
- Using gpdk180 NMOS/PMOS models to build the 6T SRAM cell  
- Sizing transistors to maintain read stability and ensure successful write operations  
- Connecting BL, BLB, WL, VDD, and ground according to SRAM architecture  
- Designing a clean, structured SRAM testbench for read/write/hold simulations  
- Running transient analysis to validate cell behavior under gpdk180 device physics  
- Ensuring correct operation across different input transitions and timing conditions
    
<div style="display:flex; justify-content:center; gap:20px;">
  <img src="https://github.com/user-attachments/assets/03b76bf8-0a72-4693-9d49-50b47e369167" width="48%">
  <img src="https://github.com/user-attachments/assets/e398e22f-4d67-432c-9aeb-944b0f474833" width="48%">
</div>

---

# Pre-Charge Circuit

The pre-charge circuit is used to initialize both bit-lines (BL and BLB) to a known voltage before every read and write operation. In the 6T SRAM architecture, both bit-lines are typically pre-charged to VDD so that the cell only needs to slightly discharge one of the lines during a read, enabling fast and differential sensing. This step ensures consistent read behavior and prevents incorrect data from being interpreted by the sense amplifier.

In gpdk180, the pre-charge block is implemented using PMOS transistors connected between VDD and the bit-lines. A control signal, usually named PRE or PRECHARGE, turns these transistors ON during the pre-charge phase. When PRE is high (or low, depending on topology), both BL and BLB are shorted to VDD through PMOS devices, equalizing them to the same initial voltage.

Once the pre-charge phase is complete, the PRE signal is deactivated, isolating the bit-lines so that the SRAM cell can perform a read or write operation without interference. Proper sizing of the PMOS devices is important to ensure fast and symmetrical charging of both lines.

Key functions of the pre-charge circuit:
- Initializes BL and BLB to VDD before each operation  
- Ensures balanced bit-lines for differential sensing  
- Prevents incorrect or unstable read results  
- Reduces read delay by providing a known starting voltage  
- Supports noise immunity and reliable sensing operation
  
<img width="910" height="471" alt="image22" src="https://github.com/user-attachments/assets/8476cb80-1831-43fa-aeea-46ba2bb79bbd" />

---

# Sense Amplifier

The sense amplifier is responsible for detecting the small voltage difference that appears between BL and BLB during a read operation. In a 6T SRAM cell, the voltage swing on the bit-lines is intentionally kept small to achieve faster read access. Because the difference is too small for direct digital interpretation, a sense amplifier is required to amplify it into a full logic '0' or '1'.

In this implementation, a differential sense amplifier is used, as supported by the gpdk180 technology. The amplifier compares the voltages of BL and BLB and quickly drives its output to the corresponding logic level. Before activation, both bit-lines are pre-charged to VDD by the pre-charge circuit. When the read operation begins, the cell slightly discharges one of the bit-lines, creating a voltage difference that the sense amplifier can detect.

The sense amplifier is enabled only after sufficient differential voltage develops on the bit-lines. This ensures correct and stable detection, avoids metastability, and prevents premature latching of incorrect values. Proper device sizing and enabling sequence are critical for reliable operation.

Key functions of the sense amplifier:
- Detects small voltage differences between BL and BLB  
- Converts differential inputs into a full logic output  
- Ensures fast and reliable read operations  
- Enhances noise immunity during memory access  
- Reduces overall read time by amplifying small bit-line variations  

 SENSE AMPLIFIER CIRCUIT DIAGRAM

 ---

 # Write '0' Operation

Writing a ‘0’ into the SRAM cell means forcing the internal node Q to 0 V and QB to VDD.  
To achieve this, the bit-lines are driven such that:

- **BL  = 0 (GND)**
- **BLB = 1 (VDD)**

When the word line (WL) goes high, the access NMOS transistors turn ON and connect the bit-lines to the internal nodes. The strong BL = 0 driver pulls node Q down to ground through the access transistor. As Q falls below the switching threshold of the cross-coupled inverter, the opposite node QB is automatically driven high through feedback.

This feedback action “locks in” the new data and ensures the cell holds the value even after WL is deasserted. Because writing a ‘0’ relies on the **NMOS pull-down path**, it is generally stronger and easier to perform in gpdk180 technology.

Key points:
- BL pulls Q to 0 V through the access transistor  
- The inverter pair amplifies the voltage change, driving QB high  
- Access NMOS must overpower the PMOS pull-up on Q  
- WL must remain high long enough for Q to fully switch and settle

<img width="800" height="573" alt="image6" src="https://github.com/user-attachments/assets/d92598c4-ec45-4156-86db-a6f314d4c49e" />

---

# Write '1' Operation

Writing a ‘1’ into the SRAM cell requires forcing internal node Q to VDD and QB to 0 V.  
To do this, the bit-lines are driven as:

- **BL  = 1 (VDD)**
- **BLB = 0 (GND)**

When WL is asserted, BL drives node Q toward VDD, while BLB pulls QB toward ground. However, writing a ‘1’ is usually **more difficult than writing a ‘0’** because the access NMOS transistor must pass a strong high voltage to node Q through an NMOS device, which introduces a VDS drop (VDD – Vth). Despite this, the external write driver must be strong enough to raise Q above the inverter switching threshold.

Once Q rises sufficiently, the cross-coupled inverter flips, driving QB completely low and reinforcing the stored ‘1’. WL is then lowered to isolate the cell from the bit-lines.

Key points:
- BL raises Q toward VDD through the access NMOS  
- BLB pulls QB low, helping the opposite inverter switch  
- Write '1' requires stronger drivers due to NMOS high-level degradation  
- Cell flips once Q crosses the inverter switching threshold  
- WL must remain high long enough to overcome feedback and latch the new state  

<img width="800" height="573" alt="image8" src="https://github.com/user-attachments/assets/920012cf-4468-419e-b094-22111cca64e4" />

---

# Read '1' Operation

When the SRAM cell stores a ‘1’, the internal nodes are:
- Q  = 1 (VDD)
- QB = 0 (GND)

Before the read, the pre-charge circuit sets:
- BL  = VDD  
- BLB = VDD  

During the read, WL is asserted HIGH, turning ON the access NMOS transistors.  
Since Q = 1, node Q tries to hold BL at VDD. Because the pull-up PMOS in the inverter is already strong, BL remains close to VDD with only a very small drop.

On the opposite side, QB = 0 pulls BLB slightly downward through the access NMOS transistor. This small discharge on BLB creates a **differential voltage** between BL and BLB. Even a small difference (tens of millivolts) is enough for the sense amplifier to detect and amplify the logic value.

Key points of reading ‘1’:
- BL stays near VDD because Q = 1 supports it  
- BLB discharges slightly due to QB = 0 pulling through the access NMOS  
- Voltage difference ΔV = BL – BLB is sensed by the sense amplifier  
- The cell must not flip state during the read (read stability is critical)  

<img width="1368" height="503" alt="image10" src="https://github.com/user-attachments/assets/2f148b11-e958-4a56-ab80-c773fda78174" />

---

# Read '0' Operation

When the SRAM cell stores a ‘0’, the internal nodes are:
- Q  = 0 (GND)
- QB = 1 (VDD)

Before the read, the bit-lines are precharged:
- BL  = VDD
- BLB = VDD

During the read, WL is asserted HIGH, enabling the access transistors.  
Since Q = 0, the BL connected to Q begins to discharge slightly through the access NMOS transistor. At the same time, BLB remains near VDD because QB = 1 holds it high through the cross-coupled inverter.

This creates a detectable voltage difference between BL and BLB. The sense amplifier interprets this differential voltage as a stored ‘0’.

Key points of reading ‘0’:
- BL discharges slightly because Q = 0  
- BLB stays high because QB = 1 supports it  
- ΔV = BLB – BL is amplified by the sense amplifier  
- The cell must maintain stability and not accidentally flip during the read  

<img width="1368" height="535" alt="image12" src="https://github.com/user-attachments/assets/c6d9d67b-fc9b-410a-b60e-b3d0810f17a8" />

---

# Stability of SRAM Cell

Stability of a 6T SRAM cell refers to its ability to reliably hold data ('0' or '1') without being unintentionally disturbed during hold or read operations. Since the cell is built using two cross-coupled inverters, its stability depends on the strength ratios between pull-up, pull-down, and access transistors. Any imbalance or poor sizing can lead to read disturbances, write failures, or data retention issues.

The two major stability checks performed for SRAM cells are:
1. **Hold Stability** – the ability to retain data when WL = 0.
2. **Read Stability** – the ability to avoid a read disturb when WL = 1 (most critical).

Static Noise Margin (SNM) is used as the quantitative measure of stability. SNM is calculated using the **butterfly curve**, which overlays the VTCs (Voltage Transfer Characteristics) of the two inverters and identifies the largest square that can fit between the curves. A larger SNM indicates better cell stability and robustness under noise, variations, and mismatch.

Key factors affecting stability:
- Pull-down NMOS strength  
- Access transistor sizing (WL device strength)  
- Pull-up PMOS strength  
- Bit-line capacitance and read discharge behavior  
- PVT variations in gpdk180  

---

# Hold State Butterfly Analysis

During the hold condition (WL = 0), the access transistors are OFF and the SRAM cell is completely isolated from the bit-lines. The stored value is maintained only by the cross-coupled inverters, making this the most stable state of the cell. The butterfly curve for hold state is generated by plotting the VTC of one inverter against the mirrored VTC of the other inverter.

To perform the butterfly analysis:
1. WL is kept LOW so the access transistors are disconnected.
2. A DC sweep is applied at node Q while plotting the voltage at node QB.
3. The resulting superimposed curves form the characteristic "butterfly" shape.
4. The **Static Noise Margin (SNM)** is the side length of the largest square that fits between the two VTC lobes.

In the hold state:
- The feedback loop is strongest because the cell is isolated.
- SNM is maximum because no external disturbances affect Q or QB.
- Both inverters switch at stable thresholds, giving the largest butterfly lobes.

Interpretation:
- A large hold SNM means strong data retention.
- If hold SNM is small, the cell may flip its value due to noise, leakage, or process variations.

<img width="800" height="600" alt="image16" src="https://github.com/user-attachments/assets/6a4b44ab-6277-4e02-9708-194d154f826c" />

 SNM = min (max(SNM1),max( SNM2) )
= min (578.869mV, 585.075mV)
= 578.869mV**

---

# Read State Butterfly Analysis

The read operation introduces the greatest risk of distorting the stored value. During a read, the word line (WL) is asserted, and the access transistors connect the internal nodes to the precharged bit-lines. This connection weakens cell stability because the internal node storing '0' may be pulled upward slightly through the access transistor, reducing noise margin.

To generate the read-state butterfly curve:
1. WL is set HIGH so access transistors are ON.
2. BL and BLB are precharged to VDD (as in a real read cycle).
3. A DC sweep is applied at node Q while plotting QB.
4. The resulting butterfly curve is narrower, showing reduced SNM.

In the read state:
- The node storing '0' rises slightly due to BL = VDD connected through the access transistor (read disturb).
- This upward shift reduces the inverter’s effective switching threshold.
- SNM is significantly smaller than in the hold state.

Interpretation:
- A large reduction in SNM indicates poor read stability.
- If SNM becomes too small, the cell may **accidentally flip** during a read (read disturb failure).
- Proper sizing ratio (pull-down > access > pull-up) is essential to maintain good read SNM.

Read SNM is one of the most important reliability metrics in SRAM design because it directly determines whether the memory can perform safe reads without destroying data.

<img width="800" height="600" alt="image18" src="https://github.com/user-attachments/assets/2837d1be-babb-4bbe-90b3-84c1e183ed75" />

 SNM = min (max(SNM1),max( SNM2) )
 = min (225.417mV, 218.623mV)
 = 218.623 mV
 
---

# Sense Amplifier Analysis

The simulation was conducted for read operation to find the time it takes to sense 200 mV sense margin ( difference in voltage between BL and BL' after WL is made HIGH )

<img width="1388" height="698" alt="image28" src="https://github.com/user-attachments/assets/bc213d7b-8d9e-4fe0-be8f-335fc44fa616" />

The circuit took 86.17ps to sense the 200mV sense margin.

---

# Conclusion

The design and simulation of the 6T SRAM cell in gpdk180 technology demonstrates the fundamental operation and reliability challenges of static memory structures. By implementing the cell at the transistor level, building supporting circuits such as the pre-charge block and sense amplifier, and analyzing read, write, and hold operations, the project verifies the complete functionality of a single-bit SRAM memory cell.

Through detailed transient simulations, it is shown that the cell can correctly perform write '0', write '1', read '0', read '1', and stable hold operations under proper bit-line and word-line control. Stability analysis using butterfly curves confirms that the hold SNM is higher, while the read SNM is reduced due to the access transistors and bit-line coupling, highlighting the critical importance of device sizing ratio (pull-down > access > pull-up) for robust memory operation.

Overall, this project provides a clear understanding of how 6T SRAM cells behave electrically, how stability is affected during different operations, and how supporting circuits contribute to reliable data sensing. The results validate the design approach and form a solid foundation for scaling the cell into larger memory arrays or exploring advanced optimization techniques in future work.

---
