# Conceptual Design: 64-Channel TDM Audio Interface

## Introduction

Contributor: Adam

The system defined in this document at its most basic level is a 64 channel in, 64 channel out Analog to Digital/Digital to Analog Converter (AD/DA). On the AD side, analog signals coming from sources such as a microphone preamplifier or a keyboard, are digitized and sent to a PC running commercial recording software known as a Digital Audio Workstation (DAW). The DA side can take a digital signal from the PC, convert it to analog and send it to an external piece of analog equipment such as an equalizer or reverb unit. 

On top of this, the AD side will offer digitally controllable analog filters and DSP filters. This allows the user to benefit from the zero latency offered by the analog filters, and/or the extreme filter shapes offered by DSP. This all happens locally on the device, reducing the load on the computer's processor, which would typically perform DSP from within the DAW. 

While 64x64 converters exist on the market now, they are extremely expensive (to the tune of $10,000 and up). There are smaller systems on the market which can be combined together, but these rely on external interface devices which increase cost and reduce reliability. This project aims to unify everything a user might need into one package, while also reducing cost. 

In the body of this document, further details will be discussed relating to the budget and scope of the project as well as why this project should exist in the first place. The team will also provide minimum requirements to deem the project a success, as well as options and workarounds for common issues that can be anticipated when developing a product from the ground-up. 

## Formulating the Problem

Contributors: Adam, David

In this section, the following questions are considered:

* Who does the problem affect?  
  * Commercial Recording Studios  
  * Broadcasting Studios  
  * Independent Recording Artists  
  * Churches (1500+ members)  
* Why is this solution needed?  
  * Exponential price gap between pro-sumer and professional markets  
  * Very difficult to find a solution that is entirely self-contained  
  * This project aims to reduce or completely eliminate any proprietary hardware/software formats  
* What challenges necessitate a dedicated, multi-person engineering team?  
  * Multiple engineering specialties are needed ranging from hardware, software and mechanical  
    * Hardware specialization is needed for circuit board layout, parts specifications, and circuit design  
    * Software specialization is needed to have all of the hardware systems communicate between each other. The ADC and DAC systems must communicate with the FPGA using the TDM communication format, and the FPGA must communicate with a host PC using ethernet.   
    * Mechanical specialization is needed for constructing the device. This ranges from the size of the circuit boards to reduce cost, 3d printing mechanical standoffs, and any other items needed to facilitate the physical construction of the device  
  * Time constraints  
    * The timeline for this project is drastically reduced compared to a project in the commercial world. Coupled with only 3 team members, every team member must be able to switch jobs requiring different areas of expertise in order to complete the project.   
* Why aren't off-the-shelf solutions sufficient?  
  * None of the solutions on the market implement digitally controllable analog filters   
  * The few off the shelf solutions that do exist are extremely expensive and still require external interfaces  
    * Example: The Antelope Orion Galaxy 64 is $10,000, requires a Dante PCIe card ($1500). Antelope is also infamous for having poor software support  
    * Example: The RME M32 can be purchased in either AD or DA conversion. To achieve a 64x64 count, four units in total would need to be purchased at a cost of $4000 each, for a total cost of $16,000 

### Background

Contributor: Adam

The project device has three subsystems which have varying degrees of complexity. In order of complexity: FPGA motherboard, ADC board, and DAC board. Depending on budgetary restrictions, some of these systems may need to be reduced in scope. 

Since JLCPCB is the intended manufacturer of PCBs, the minimum order quantity is 5 boards. Therefore, the most cost-effective (and least wasteful) option is to design the project around four 16-channel PCBs for the ADC and DAC designs. This way, one minimum quantity order will yield enough boards to populate one full prototype unit, with a spare PCB leftover for the ADC and DAC each. 

The DAC boards will have a minimum amount of components on them, as they will consist of an MCU, two 8-channel DAC chips, a precision reference IC, and 16 quad opamps plus the associated R/L/C values. These boards can very likely all be built to completion. 

The ADC boards have a high number of peripheral components related to the analog filters. This will necessitate the inclusion of many shift registers, multiplexers and digipots. The chosen ADC chips also only come in 4-channel variants, so a single 16-channel board will require 4 ADC ICs. Therefore, depending on budgetary constraints, it may be necessary to populate less of the filters. In an extreme case, it may be necessary to also reduce the number of ADCs. A good balance is likely related to populating all 64 channels worth of ADCs, with perhaps 2-8 analog filters. 

The FPGA motherboard is a high-priority stretch goal. Since the team already owns an AC701 evaluation board, the project can be completed in its entirety only using the ADC, DAC and AC701 boards. However, the team has a large desire to manufacture an FPGA board, as it would be an extremely powerful career highlight for entering industry. This presents two potential options for accomplishing the motherboard design:

* Go with the same FPGA on the evaluation board. This chip alone is $330, but yields the highest likelihood of success, considering the reference design from the AC701 can be used.  
* Go with a cheaper chip. Depending on the design requirements, it may be possible to use a less powerful chip. The only caveat to this approach is that there may be a higher risk of a catastrophic PCB design failure, since a chip-specific reference design may not exist. 

With the scenarios for each subsystem described, the team believes it is possible to achieve an end product that will give the most experience possible from this design sequence, leading to job positions beyond entry level after graduation. It is for this reason that the FPGA motherboard, while a stretch goal, should still be attempted.

### Specifications and Constraints

Contributors: Adam, David, Zachary

#### **Specifications**

Specifications are requirements imposed by **stakeholders** to meet their needs. If a specification seems unattainable, it is necessary to discuss and negotiate with the stakeholders. The team is currently in talks with two companies who are interested in becoming stakeholders; the specifications below may be revised pending the outcome of those discussions. The following shall statements represent the minimum viable requirements as defined by the engineering team.

1. The system shall provide a minimum of 64 analog input channels and 64 analog output channels.
2. The system shall support sample rates of 44.1 kHz and 48 kHz at a minimum, with 96 kHz and 192 kHz as stretch goals.
3. The system shall use 24-bit audio resolution on both the ADC and DAC paths.
4. The system shall communicate audio data to and from a host PC using Gigabit Ethernet.
5. The system shall implement Time-Division Multiplexing (TDM4) for all audio data transfer between codec boards and the FPGA.
6. The system shall provide digitally controllable analog filters on the ADC input path, controllable via SPI from a local microcontroller.
7. The system shall implement real-time DSP processing (parametric EQ, dynamics, mixing) on the FPGA, offloading processing from the host DAW.
8. The system shall achieve a total analog-to-digital-to-analog latency of less than 5 ms at 48 kHz.
9. The system shall not exceed a total bill of materials cost of $2,000 for one complete prototype unit.
10. The system shall be powered from a single external DC power supply (12V nominal).
11. The system shall provide relay-based true bypass for each analog filter section to ensure signal integrity when filters are not in use.

#### **Constraints**

Contributors: David, Zachary 

The following standards and constraints shall be applicable to this project. For each, the origin of the constraint and its impact on the design are noted.

**Audio and Safety Standards:**

* **IEC 62368-1** [1] -- This standard sets the requirements for audio/video, information, and communication technology equipment safety. It applies to this project because the system is a mains-powered audio device that will be operated by end users in studio environments. This standard drives the use of an external Class II DC power supply (12V) rather than an internal mains-to-DC converter, ensuring all user-accessible voltages remain below hazardous energy thresholds. *(Origin: regulatory / public safety)*

* **CISPR 32** [2] -- The CISPR 32 is the international EMC standard for electromagnetic emission requirements of multimedia equipment (MME) with an R.M.S. voltage not exceeding 600V. This standard applies because the system contains high-speed digital clocks (TDM bit clocks, SPI buses, FPGA I/O) in close proximity to sensitive analog audio circuitry. Compliance informs PCB layout practices including separated ground planes, controlled impedance routing, and ferrite filtering on power entry. *(Origin: regulatory / electromagnetic compatibility)*

* **AES3** [3] -- Defines the digital audio interface format from which the TDM4 framing protocol used in this system is derived. All serial audio data transfer between codec boards and the FPGA shall conform to the bit-packing and word-select conventions specified in AES3. *(Origin: industry standard / interoperability)*

* **AES17** [4] -- Specifies measurement methods for digital audio equipment including THD+N, frequency response, crosstalk, and dynamic range. The system's audio performance shall be validated using measurement techniques consistent with AES17. *(Origin: industry standard / verification methodology)*

**Design and Manufacturing Constraints:**

* **JLCPCB Manufacturing Rules** -- All PCB designs shall conform to JLCPCB's standard process capabilities (minimum trace width, via size, layer count, etc.) to maintain cost-effective fabrication. Minimum order quantity of 5 boards per design constrains the board partitioning strategy (4x 16-channel boards).

* **RoHS Compliance** -- All components shall be RoHS compliant. Lead-free solder processes shall be used for all PCB assembly.

* **Budget Constraint** -- The total prototype cost shall not exceed the allocated capstone budget. Component selection shall favor parts with strong domestic (USA) availability to avoid supply chain delays within the project timeline.

* **Timeline Constraint** -- The project shall be completed within the two-semester senior capstone sequence (Fall 2025 - Spring 2026). Critical path items (evaluation board, FPGA HDL) shall be prioritized to allow maximum integration testing time.

## Comparative Analysis of Potential Solutions

Contributors: Adam, David, Zachary

In this section, various potential solutions are examined, design trade-offs are discussed, and factors influencing the selection of each subsystem solution are outlined. For each major design decision -- embedded toolchain, analog filter methodology, digital filter implementation, FPGA platform, power architecture, PCB strategy, and host interface -- the alternatives considered and the rationale for the chosen approach are presented.

### Embedded Toolchain: Microchip 32-bit PIC

For all elements requiring the use of a microcontroller, the Microchip PIC platform was chosen. The major reasons are listed below: 

* PICs have a long history of reliability  
* The team already possesses the tool to flash the chip (PICKIT 5\)  
* Cheaper than STM counterparts  
* USA availability is better    
* Previous use in other projects

### Analog Filter Methodologies

For digital control of analog signals, there are two major methodologies: adaptation of existing analog filters (i.e., mechanical rheostat or potentiometer control), or Multiplying DAC (MDAC) controlled state variable filter (SVF). Here, the pros and cons of each method are examined. While the adaptation method was ultimately chosen, the MDAC method will be used in the event that the adaptation fails in the prototyping stage. 

**MDAC**

The MDAC based filter can be used to give a state variable filter with digitally controllable gain, Q factor and frequency selection. This is by far the most versatile analog filter, since all parameters can be digitally controlled. The major drawback though, is the immense cost associated with a MDAC capable of accepting large amplitude, bipolar signals. A single SVF low pass filter requires the use of 4 MDACs. The TI DAC8814 is a quad 16-bit DAC which starts at a base cost of $41. A SVF using the MDAC design can traverse the entire audio spectrum with ~3 points per Hz due to the 16-bit resolution. 

While a state variable filter will have a high pass, low pass and band pass output, the control signals affect all 3 outputs simultaneously. This is undesirable for a use-case in which a user may want both a high and low pass filter targeted at different frequencies. Thus, to populate a 5-band HPF/LPF/Para1/Para2/Para3 equalizer, a total of 5 quad MDACs would be needed, at a cost of $205 per-channel.


**Adaptation**

Another method for a digitally controllable analog filter, is simply adapting an existing topology for digital control. This differs from the MDAC method, because while MDACs are used as programmable resistor ladders, the MDAC resistor ladders are only tolerant to themselves, and a discrete resistance cannot be relied upon or determined easily. This is to say that the resistances are ratiometric, and not ohmic. 

While MDACs are an ideal digital control medium for analog signals, adaptation to existing classic and beloved analog filters is not feasible because of the previously mentioned resistance unpredictability. Thus, the next-best method is the use of digipots in existing filter topologies. Digipots do not have the same resolution as MDACs and top out at 10-bits of resolution, with 8-bits being the norm. Digipots also have known resistances, and can essentially be treated as a drop-in replacement for a mechanical potentiometer. 

A major obstacle for using digipots is related to the resolution. As previously mentioned, a 16-bit MDAC filter can traverse the entire audio spectrum. A 10-bit digipot *can* traverse the entire audio spectrum, however due to the reduced bit-depth, large gaps can happen in frequency selection. Beyond that, digipots only come in linear taper configurations. Typically in a potentiometer-based design, logarithmic (and reverse log) controls are used for frequency selection, and the bandwidth traversal is limited (i.e., the pot will only sweep from 500 Hz to 5 kHz). Simply replicating this with a digipot is trivial, and would be more of a novelty. Instead, to make the use of digipots viable, and an objective improvement over their mechanical counterparts, capacitor arrays can be used to change the bandwidth that the digipots traverse over. Using an 8:1 multiplexer allows the digipot to multiply its effective resolution by 8. While 13 bits of resolution is still not as detailed as the 16-bit MDAC, simulations show that frequencies below ~8 kHz will have multiple data-points per-Hz and the higher frequencies will most likely have an inaudible adjustment discontinuity (in the tens of Hz range). 

The digipot design centers around the Harrison 32C mixing console's channel strip equalizer section. This is a highly desirable 5-band EQ which has been used to record and mix hundreds of commercially successful films and records. This is a much more economically viable option, as the digipots can be bought for $2.10 each. Rather than needing 4 control elements per-filter, the high and low-pass filters only need 2 digipots, and the three parametrics only need 3. Thus, the cost is only $27.30 for 13 digipots for all 5 filters.   

### Digital Filter Methodologies

Contributor: David

In addition to the analog filters described above, the system implements a second layer of filtering entirely in the digital domain on the FPGA. Where the analog filters provide zero-latency tonal shaping in the Harrison 32C tradition, the DSP filters offer capabilities that are impractical or impossible to achieve in analog circuitry: arbitrarily steep filter slopes, perfectly repeatable coefficient sets, per-channel dynamics processing, and bus-level mixing -- all without adding physical components per channel.

**IIR Biquad Filters (Parametric EQ and HPF)**

The core DSP filter primitive is the second-order IIR biquad, implemented in Direct Form I (DF1) [22] on the Artix-7's DSP48E1 multiply-accumulate units [12]. Each biquad requires 5 multiply-accumulate operations and consumes 7 clock cycles at the DSP clock rate of 300 MHz. DF1 was selected over the more common Direct Form II and Transposed Direct Form II specifically for coefficient stability at high sample rates. At 192 kHz, IIR feedback coefficients for low-frequency filters cluster extremely close to z = 1.0 (for example, a 100 Hz HPF at 192 kHz yields a1 ~= -1.99935). DF1 separates the feedforward and feedback paths, providing better numerical behavior under the 18-bit coefficient quantization imposed by the DSP48E1 B-port width.

Filter coefficients are computed in real-time by the control-plane MicroBlaze processor using the Bristow-Johnson audio EQ cookbook formulae [15]. This allows the host software to send high-level parameters (center frequency, gain, Q factor, filter type) and have the FPGA compute the corresponding biquad coefficients locally, avoiding the need to transmit raw coefficient arrays over Ethernet.

The per-channel DSP chain provides a 1-biquad high-pass filter (DC removal, 20-300 Hz selectable cutoff) followed by a 4-band fully parametric EQ. Each parametric band can operate as a bell, shelf, or notch filter. All five biquad stages are independently bypassable. The total processing cost for the EQ section is 35 DSP clock cycles per channel (5 biquads x 7 cycles each).

**Dynamics Processing (Noise Gate and Compressor)**

Following the EQ chain, each channel passes through a noise gate and a compressor. These are implemented as log-domain envelope followers with threshold comparison and smoothed gain ramps. The noise gate requires 4 DSP cycles per channel and the compressor requires 8 DSP cycles per channel. Attack, release, threshold, and ratio parameters are all configurable from the host via the same coefficient pipeline used for EQ parameters. The compressor uses a soft-knee characteristic with configurable knee width to avoid audible gain pumping artifacts.

**Mixing and Bus Processing**

After per-channel processing, the DSP engine accumulates all 64 input channels into output buses using the DSP48E1 cascade chain. The mixing phase supports 66 buses (64 main outputs plus pre-fader listen and after-fader listen). Bus-level processing includes a compressor, limiter, and optional multiband dynamics.

**Output Stage: TPDF Dither and Brick-Wall Limiting**

The final DSP stage applies Triangular Probability Density Function (TPDF) dither [23] before truncation from the internal processing word width down to the 24-bit DAC output word. TPDF dither is the industry-standard method for eliminating quantization distortion artifacts during word-length reduction. A brick-wall limiter follows the dither stage to prevent digital clipping on the output path.

**Complementary Relationship to Analog Filters**

The analog and digital filter paths are complementary rather than redundant. The analog filters (Harrison 32C topology with digipot adaptation) operate on the input signal before digitization, providing zero-latency tonal shaping that is musically desirable for live monitoring and tracking. The DSP filters operate post-digitization and provide the precise, recallable, and computationally intensive processing (dynamics, mixing, bus processing) that would normally be performed by the host DAW. The relay bypass on the analog filter section allows the user to select analog-only, digital-only, or combined processing per channel.

### FPGA Selection

Since the team is using the AC701 evaluation board [10], the FPGA being used is the Artix-7 XC7A200T-2FBG676C [11]. This is an expensive chip, at $330. This chip is certainly capable of achieving all of the goals of this project, however once the HDL/software side of this project is complete, it is very likely a *much* cheaper FPGA can be used. However, until the HDL/software side of the project concludes, it will not be possible to determine which FPGA that may be. The XC7A200T provides 740 DSP48E1 slices, 365 BRAM36 blocks, 134,600 LUTs, and 400 user I/O pins -- well in excess of the system requirements.

### Embedded Power System

Contributor: Zachary

**Evaluation Board Power:**

The evaluation board uses a simpler linear regulator topology compared to the production FPGA motherboard. An AZ1117-3.3 [19] provides the 3.3 V rail for digital logic and the MCU, an AZ1117-1.2 provides the 1.2 V ADC core supply, and an LM317 [20] / LM337 [21] pair generates the +/-15 V split rails required for the analog opamp stages (MC33079). All regulators related to digital logic accept the 12 V DC input, and all regulators related to analog signal accept +/- 18 V. Separate analog and digital ground planes are maintained on the PCB and connected at a single star-ground point to minimize digital noise coupling into the audio path.

**Production FPGA Motherboard Power (Stretch Goal):**

For the different DC voltage values needed for the FPGA, the FS1412\-0600-AS and FS1006\-3300-AL DC/DC Converters were used. A major benefit is the ability to have a converter feedback to the next one to have a startup sequence without a sequencing chip. A design choice for the FS converters is the lower cost compared to LMZ31710 and LMZ3150X DC/DC converters given the constraints of the high current draw needed for the FPGA. A design consideration for the power system was to have a DC/DC converter for each of the unique power requirements for the FPGA. But for this power system it was decided to run the same voltage power requirements on the same DC/DC converter with a higher current draw. The FS chip can supply 12 A which can handle that type of current draw. This design saves space on the PCB and reduces overall cost for the power system.

### PCB Design Strategy

Due to lead times and cost, the PCB design will be split up into three main projects: 

1. **Evaluation board:** This will be a PCB containing a single 4-channel ADC, a reduced-count analog filter section and a single 8-channel DAC. This will give a prototyping platform for testing and verifying the various elements of the project. It will allow the team to write code to test the ADC/DAC, build digipot circuits and interface with the FPGA evaluation PCB
2. **16-channel AD/DA PCB**: This will be the next evolution of the evaluation board. Once any design quirks are worked out on the cheaper evaluation board, the production level PCBs will be created. These boards will have 16 channels A/D. This was deliberately done, considering the minimum PCB order is 5 boards. This allows a minimum board order to populate all 64 channels with a spare left over.
3. **FPGA PCB**: Once the production PCBs work with the FPGA evaluation board, a custom FPGA board will be manufactured to complete the system. The system will be complete and working before the design of this board. The purpose behind this board is a production level design. It may be possible to use a much cheaper FPGA in this board, although that cannot be determined until the evaluation board is working with the AD/DA boards. 

### Host Interface: Ethernet and OSC

Contributor: David

The host PC interface is responsible for transporting audio data between the FPGA and the DAW, as well as carrying all control and configuration messages. Three interface options were evaluated: USB, Thunderbolt/proprietary PCIe, and Gigabit Ethernet.

USB Audio Class 2.0 supports high-channel-count audio, but the Artix-7 XC7A200T does not include a USB transceiver, so an external USB PHY and a compliant USB device stack would be required. This adds hardware cost, firmware complexity, and a dependency on proprietary IP cores. Thunderbolt and proprietary PCIe solutions (such as those used by Antelope and Avid) require licensed host-side drivers and additional interface hardware, directly contradicting the project's goal of eliminating proprietary dependencies.

Gigabit Ethernet was selected because the AC701 evaluation board [10] already provides a Marvell 88E1116R Gigabit Ethernet PHY connected to the FPGA via RGMII, requiring zero additional hardware. Ethernet is a standard, vendor-neutral transport with mature open-source IP stacks (lwIP [25]) and MAC implementations. At 1 Gbps, Ethernet provides more than sufficient bandwidth for 64 channels of 24-bit audio at 48 kHz (approximately 73.7 Mbps uncompressed), with substantial headroom for control traffic, metering data, and higher sample rates.

For the Ethernet MAC implementation, two options were evaluated. The Xilinx Tri-Mode Ethernet MAC (TEMAC) IP core provides full-featured DMA and hardware checksum offload, but requires a paid license ($4,920 per site). The Forencich verilog-ethernet open-source MAC [24] is MIT-licensed, synthesizes on the Artix-7 without modification, and has been validated in comparable FPGA audio projects. The Forencich MAC was selected for the initial implementation to avoid licensing costs; migration to TEMAC remains possible if DMA performance proves insufficient at higher sample rates.

The control protocol uses Open Sound Control (OSC) over UDP. OSC is an open standard widely supported by DAWs and control surfaces, enabling the system to interoperate with existing studio infrastructure without custom host-side software. Parameter messages (EQ frequency, gain, compressor threshold, relay bypass state) are encoded as OSC bundles and dispatched by the SYS_MB MicroBlaze processor on the FPGA.

## High-Level Solution

This section presents the comprehensive, high-level solution for the 64-channel TDM audio interface. The design is organized around three physical PCBs (evaluation board, production AD/DA boards, and a stretch-goal FPGA motherboard) connected to the Xilinx AC701 evaluation board. The system provides a complete signal path from analog input through digitally-controlled analog filtering, analog-to-digital conversion, FPGA-based DSP processing and mixing, digital-to-analog conversion, and analog output -- all controlled by a host PC over Gigabit Ethernet.

### Hardware Block Diagram

Contributors: David, Adam

The hardware block diagram below illustrates the complete system architecture. Each block represents an atomic subsystem, and each connection indicates the signal type and direction of data flow.

```
+-------------------------------------------------------------------------+
|                        EVALUATION BOARD PCB                             |
|                                                                         |
|  +----------+    +----------+   +-----------+   +------------------+    |
|  |  INPUT    |   |  HP/LP   |   |PARAMETRIC |   |   ADC            |    |
|  |  STAGE    |-->| FILTERS  |-->|   EQ      |-->|  (ES9842Q PRO)   |    |
|  |(Buffers,  |   |(Digipots,|   |(Digipots, |   |  4-ch per IC     |    |
|  | Gain,     |   | MCP41U83,|   | MC33079,  |   |  TDM4 output     |    |
|  | MC33079)  |   | 8:1 MUX, |   | Capacitor |   |                  |    |
|  |           +   | MC33079) |   | arrays)   |   |                  |    |
|  |  Relay    |<--|          |   |           |   |                  |    |
|  |  Bypass   |   |  Relay   |   |  Relay    |   |                  |    |
|  +-----------+   |  Bypass  |   |  Bypass   |   +---------+--------+    |
|       ^          +----------+   +-----------+             | TDM4        |
|       |                ^               ^                  | Serial      |
|  Analog In            |               |                   | (BCLK_AD,   |
|  (Balanced/           |   SPI Bus 1   |                   |  WS_AD,     |
|   Unbalanced)         |   (SCK, MOSI, CS)                 |  DATA)      |
|                       |               |                   |             |
|  +--------------------+---------------+------+            |             |
|  |              MCU (PIC32MM0256GPM064)      |            |             |
|  |  SPI Master 1 -> Filter/digipot/mux ctrl  |            |             |
|  |  SPI Master 1 -> ADC configuration        |            |             |
|  |  SPI Slave 2  <- FPGA commands            |            |             |
|  |  Level Xlat: SN74AVC4T245PW (3.3V<->2.5V) |            |             |
|  +-----------------------+-------------------+            |             |
|                          | SPI Slave                      |             |
|  +---------------+       |                                |             |
|  |  CLOCK GEN    |       |                                |             |
|  | 549CCAC 10MHz |       |                                |             |
|  | + Ext BNC In  |-------+-------MCLK-------------------->|             |
|  +---------------+       |                                |             |
|                          |                                |             |
|  +--------------------+  |                                |             |
|  |   POWER SUPPLY     |  |                                |             |
|  | 12V DC In          |  |                                |             |
|  | AZ1117-3.3 -> 3.3V |  |                                |             |
|  | AZ1117-1.2 -> 1.2V |  |                                |             |
|  | LM317     -> +15V  |  |                                |             |
|  | LM337     -> -15V  |  |                                |             |
|  +--------------------+  |                                |             |
|                          |                                |             |
|  +------------------+    |             +--------------+   |             |
|  |   DAC            |    |             |  SPI FANOUT  |   |             |
|  |  (ES9017S)       |<---+--TDM4-------|  (active CS  |<--+             |
|  |  8-ch per IC     |    |  Serial     |  distribution|   |             |
|  |  MC33079 output  |    |  (BCLK_DA,  |  to all SPI  |   |             |
|  |  stage           |    |   WS_DA,    |  peripherals)|   |             |
|  +--------+---------+    |   DATA)     +--------------+   |             |
|           |              |                                |             |
|      Analog Out          |                                |             |
|                          |                                |             |
+--------------------------+--------------------------------+-------------+
                           | SPI (SCK 2, SDI2, SDO2, SS)    | TDM4 (x2 links)
                           |                                | BCLK, WS, DATA IN/OUT
                           v                                v
               +-------------------------------------------------------+
               |          FPGA -- AC701 EVALUATION BOARD               |
               |          Artix-7 XC7A200T-2FBG676C                    |
               |                                                       |
               |  +-------------+  +----------------+  +-----------+   |
               |  | TDM4 RX/TX  |  | DSP Engine     |  | MicroBlaze|   |
               |  | Serializers |->| (DSP48E1 MAC   |  | Control   |   |
               |  | + Async     |  |  array: HPF,   |  | Plane     |   |
               |  |   FIFOs     |  |  PEQ, Gate,    |  | (RT + Sys)|   |
               |  |             |  |  Comp, Mixer)  |  |           |   |
               |  +-------------+  +----------------+  +-----+-----+   |
               |                                              |        |
               |  +------------------+   +----------------+   |        |
               |  | DDR3 Memory      |   | Gigabit        |<--+        |
               |  | (AC701 onboard)  |   | Ethernet       |            |
               |  | Model/buffer     |   | (88E1116R PHY) |            |
               |  | storage          |   | RGMII -> Host  |            |
               |  +------------------+   +--------+-------+            |
               |                                  |                    |
               +----------------------------------+--------------------+
                                                  | Gigabit Ethernet
                                                  v
                                          +--------------+
                                          |   HOST PC    |
                                          |  DAW + OSC   |
                                          |  Control     |
                                          +--------------+
```

**Connection Summary:**

| Connection | Signal Type | Direction | Protocol/Notes |
|---|---|---|---|
| Analog In -> Input Stage | Analog audio | Input | Balanced or unbalanced, line level |
| Input Stage -> HP/LP Filters | Analog audio | Unidirectional | Relay-bypassable |
| HP/LP Filters -> Parametric EQ | Analog audio | Unidirectional | Relay-bypassable |
| Parametric EQ -> ADC | Analog audio | Unidirectional | Differential pair to ES9842Q PRO |
| MCU -> Digipots/MUX | Digital control | Output | SPI Master (SCK 1, MOSI 1, per-device CS) |
| MCU -> ADC Config | Digital control | Output | SPI (shared bus, dedicated CS per ADC IC) |
| FPGA -> MCU | Digital control | Bidirectional | SPI (FPGA master, MCU slave on SPI port 2) |
| ADC -> FPGA | Digital audio | Output | TDM4 serial (BCLK_AD, WS_AD, TDM IN 1-2) |
| FPGA -> DAC | Digital audio | Output | TDM4 serial (BCLK_DA, WS_DA, TDM OUT 1) |
| DAC -> Analog Out | Analog audio | Output | MC33079 I/V + output buffer |
| Clock Gen -> ADC/DAC/FPGA | Clock | Output | MCLK_AD, MCLK_DA (10 MHz nominal, ext. sync via BNC) |
| FPGA -> Host PC | Digital audio + control | Bidirectional | Gigabit Ethernet, OSC protocol |
| 12V DC -> Power Supply | Power | Input | LDOs generate 3.3V, 1.2V, 2.5V, +/-15V |

### Operational Flowchart

The following flowchart describes the system from the user's perspective, showing the sequence of actions required to set up and operate the device.

```
+---------------------+
|  User powers on     |
|  the system (12V)   |
+---------+-----------+
          v
+---------------------+
| Power-on self-test  |
| (FPGA boots,        |
|  MCU initializes,   |
|  clocks lock)       |
+---------+-----------+
          v
+---------------------+
| Connect Ethernet    |
| cable to host PC    |
+---------+-----------+
          v
+---------------------+
| Launch DAW on host  |
| PC; select device   |
| as audio interface  |
+---------+-----------+
          v
+---------------------+
| Connect audio       |
| sources to analog   |
| inputs (mic pre,    |
| keyboard, etc.)     |
+---------+-----------+
          v
+--------------------------+    +--------------------------+
| (Optional) Configure     |    | (Optional) Configure     |
| analog filters via       |--->| DSP parameters via       |
| host OSC control panel:  |    | host OSC control panel:  |
| - HP/LP cutoff freq      |    | - Digital PEQ bands      |
| - Parametric EQ bands    |    | - Gate threshold/ratio   |
| - Enable/bypass relays   |    | - Compressor settings    |
+----------+---------------+    | - Bus routing/levels     |
           |                    +----------+---------------+
           v                               v
+----------------------------------------------------------+
|                  NORMAL OPERATION                        |
|                                                          |
|  Audio In -> [Analog Filters] -> ADC -> FPGA DSP -> DAC  |
|                                  <->                     |
|                            Host PC (DAW)                 |
|                                                          |
|  - Audio streams to/from DAW over Ethernet               |
|  - DSP processing runs on FPGA in real-time              |
|  - User adjusts parameters from DAW or OSC GUI           |
|  - All settings recallable via scene management          |
+------------------------------+---------------------------+
                               v
                    +---------------------+
                    |  User powers off    |
                    |  the system         |
                    +---------------------+
```

## Atomic Subsystem Specifications

Based on the high-level design, the following sections provide comprehensive specifications for each atomic subsystem. For each subsystem, functional descriptions, interfaces, communication protocols, and detailed shall statements are provided.

### 1. Input Stage Subsystem

**Functional Description:** The input stage receives analog audio signals from external sources (microphone preamplifiers, instruments, line-level devices) and conditions them for downstream processing. It provides impedance buffering, initial gain adjustment, and relay-selectable signal routing to either the analog filter chain or directly to the ADC (true bypass). The input stage is built around the MC33079 low-noise quad operational amplifier [16] and EC2-5NU signal relays.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| Analog Audio In | Analog audio | Input | Coaxial connector (BNC on eval board), accepts balanced or unbalanced line-level signals |
| To HP/LP Filter | Analog audio | Output | Routed via relay when filters are engaged |
| To ADC (bypass) | Analog audio | Output | Direct path when relay bypass is active |
| Relay Control | Digital control | Input | GPIO from MCU via SPI fanout, controls EC2-5NU relays |
| Power | +/-15V, GND | Input | From power subsystem |

**Shall Statements:**

1. The input stage shall buffer the incoming analog signal with an input impedance of no less than 10 kohm.
2. The input stage shall provide a signal path with a noise floor at least 10 dB below the noise floor of the ADC (ES9842Q PRO).
3. The input stage shall include relay-based true bypass (EC2-5NU) allowing the signal to route directly to the ADC, bypassing all analog filter stages.
4. The input stage shall use the MC33079 quad operational amplifier for all active gain stages.
5. The input stage shall accept input signals up to +24 dBu without clipping.

### 2. High-Pass / Low-Pass Analog Filter Subsystem

**Functional Description:** The HP/LP filter subsystem provides digitally controllable high-pass and low-pass analog filters adapted from the Harrison 32C console topology. Frequency selection is achieved by MCP41U83T-502E/ST digital potentiometers [9] with capacitor array decade switching via 8:1 multiplexers. This provides an effective 13-bit frequency resolution across the audio band. Each filter (HP and LP) requires 2 digipots. Relay bypass allows the entire HP/LP section to be removed from the signal path.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| Audio In | Analog audio | Input | From input stage |
| Audio Out | Analog audio | Output | To parametric EQ subsystem |
| SPI Control | Digital (SPI) | Input | SCK 1 O, MOSI 1 O from MCU; per-digipot CS lines (CH1 HP DPOT CS, CH1 LP DPOT CS) |
| Decade MUX CS | Digital (SPI) | Input | CH1 HPLP decade CS -- selects capacitor bank via 8:1 MUX |
| Relay Control | Digital | Input | Ch1_HPLP_RLY -- enables/bypasses the HP/LP filter block |
| Power | +/-15V, GNDD | Input | Analog supply for MC33079; digital supply for digipots |

**Shall Statements:**

1. The HP/LP filter subsystem shall provide independently controllable high-pass and low-pass filter sections per channel.
2. The high-pass filter shall provide a selectable cutoff frequency range of 20 Hz to 500 Hz.
3. The low-pass filter shall provide a selectable cutoff frequency range of 2 kHz to 20 kHz.
4. Each filter shall use MCP41U83T-502E/ST digital potentiometers (50 kohm, 8-bit) for frequency control via SPI.
5. Capacitor array decade switching via 8:1 multiplexer shall extend the effective resolution to a minimum of 13 bits across the filter's frequency range.
6. The HP/LP subsystem shall include relay bypass (Ch1_HPLP_RLY) to remove both filters from the signal path with no signal degradation when bypassed.
7. The filter shall use the MC33079 quad operational amplifier for all active stages.
8. The settle time of each digipot shall be verified to not produce audible artifacts during parameter changes.

### 3. Parametric EQ Analog Filter Subsystem

**Functional Description:** The parametric EQ subsystem provides three bands of fully parametric equalization adapted from the Harrison 32C topology. Each parametric band offers digitally controllable center frequency and boost/cut gain. Frequency control uses two MCP41U83T digipots (sharing a CS pin), and gain control uses one additional digipot, for a total of 3 digipots per parametric band (9 digipots for all three bands). The circuit is implemented with MC33079 quad operational amplifiers. Capacitor decade arrays extend the frequency range.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| Audio In | Analog audio | Input | From HP/LP filter subsystem |
| Audio Out | Analog audio | Output | To ADC subsystem |
| SPI Control | Digital (SPI) | Input | SCK 1 O, MOSI 1 O; per-band CS lines (CH1 Para1_F DPOT CS, CH1 Para1_G DPOT CS, etc.) |
| Decade MUX CS | Digital | Input | Ch1_PARA_Decade_CS -- selects capacitor bank for frequency range |
| Relay Control | Digital | Input | Ch1_PARAMETRIC_RLY -- enables/bypasses the parametric section |
| Power | +/-15V, GNDD | Input | Analog supply for opamps; digital supply for digipots |

**Shall Statements:**

1. The parametric EQ subsystem shall provide three independently controllable parametric bands per channel.
2. Each parametric band shall provide boost and cut of at least +/-12 dB.
3. Each parametric band shall provide a selectable center frequency spanning the range of 100 Hz to 10 kHz at minimum, extended by capacitor decade switching.
4. Each parametric band shall use 3 MCP41U83T digital potentiometers: 2 for frequency control (sharing a CS line) and 1 for gain control.
5. The parametric EQ subsystem shall include relay bypass (Ch1_PARAMETRIC_RLY) to remove all three bands from the signal path.
6. The frequency selection digipots for each band shall share a chip-select line to ensure atomic frequency updates.

### 4. ADC Subsystem

**Functional Description:** The ADC subsystem digitizes up to 4 channels of conditioned analog audio per ES9842Q PRO IC [5]. On the evaluation board, one ADC IC provides 4 channels. On the production 16-channel board, four ADC ICs will be used. The ES9842Q PRO is configured for TDM4 output mode via SPI register writes at startup. The ADC outputs a serial TDM4 data stream (24-bit samples, left-justified in 32-bit slots) synchronized to the master clock provided by the clock subsystem.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| Analog Audio In | Analog (differential) | Input | From parametric EQ (or input stage if filters bypassed); differential pair to ADC+ / ADC- |
| TDM4 Audio Out | Digital serial | Output | TDM IN 1, TDM IN 2 -> FPGA; synchronized to BCLK_AD, WS_AD |
| SPI Configuration | Digital (SPI) | Input | SCK 1, MOSI 1, ADC 1-4 CS from MCU; used for register configuration at boot |
| Master Clock | Clock | Input | MCLK_AD from clock subsystem |
| Voltage Reference | Analog | Input | ES9312Q precision LDO [7] provides VREF |
| Power | +3.3V, +1.2V (core), +5V, GND | Input | From power subsystem |

**Shall Statements:**

1. The ADC subsystem shall digitize analog audio at 24-bit resolution.
2. The ADC subsystem shall support sample rates of 44.1 kHz and 48 kHz, with 96 kHz and 192 kHz as stretch goals.
3. The ADC subsystem shall output audio data in TDM4 format (4 channels per serial link, 128 bit-clocks per frame, left-justified 24-bit in 32-bit slots).
4. Each ES9842Q PRO IC shall be individually configurable via SPI using a dedicated chip-select line.
5. The ADC subsystem shall use the ES9312Q dual low-noise linear regulator for precision voltage reference generation.
6. The ADC subsystem shall select SPI configuration mode by tying the appropriate pin to 3.3V at power-on.
7. The evaluation board shall implement one ADC IC (4 channels); the production board shall implement four ADC ICs (16 channels).

### 5. DAC Subsystem

**Functional Description:** The DAC subsystem converts digital audio from the FPGA back to analog. Each ES9017S IC [6] provides 8 channels of DAC conversion. On the evaluation board, one DAC IC provides 8 output channels. On the production board, two DAC ICs will be used for 16 channels. The DAC receives TDM4 serial audio data from the FPGA. The analog output stage uses MC33079 quad opamps for current-to-voltage conversion and output buffering.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| TDM4 Audio In | Digital serial | Input | TDM OUT 1 from FPGA; synchronized to BCLK_DA, WS_DA |
| Analog Audio Out | Analog | Output | Coaxial connectors (BNC on eval board); line-level output |
| SPI Configuration | Digital (SPI) | Input | DAC1-8 CS from MCU for register configuration |
| Master Clock | Clock | Input | MCLK_DA from clock subsystem |
| Power | +1.2V (core), +3.3V, +/-15V, GND | Input | Core digital + analog opamp supply from power subsystem |

**Shall Statements:**

1. The DAC subsystem shall convert digital audio to analog at 24-bit resolution.
2. The DAC subsystem shall accept audio data in TDM4 format from the FPGA.
3. The DAC subsystem shall support sample rates of 44.1 kHz and 48 kHz, with 96 kHz and 192 kHz as stretch goals.
4. The analog output stage shall use MC33079 quad opamps for current-to-voltage conversion.
5. The DAC subsystem shall provide a maximum output level of at least +20 dBu.
6. The evaluation board shall implement one DAC IC (8 channels); the production board shall implement two DAC ICs (16 channels).

### 6. MCU Subsystem

**Functional Description:** The PIC32MM0256GPM064 microcontroller [8] serves as the local control processor on each ADC board. It manages all SPI peripherals on the board: configuring ADC registers at boot, setting digipot values in response to commands from the FPGA, controlling multiplexer decade select lines, and toggling relay bypass GPIOs. The MCU communicates with the FPGA via a secondary SPI port operating in slave mode. A SN74AVC4T245PW 4-bit bidirectional voltage translator [17] bridges the MCU's 3.3V I/O to the FPGA's 2.5V I/O standard. The MCU does not participate in the TDM audio data path.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| SPI Master (Port 1) | Digital (SPI) | Output | SCK 1, MOSI 1 -> digipots, MUX, ADC config; multiple CS lines via fanout |
| SPI Slave (Port 2) | Digital (SPI) | Input | SCK 2, SDI2, SDO2, SPI2_SS, SPI2_SLAVE_BUSY -> from FPGA |
| GPIO (Relay Control) | Digital | Output | Ch1_HPLP_RLY, Ch1_PARAMETRIC_RLY, etc. via fanout shift registers |
| Level Translation | Digital | Bidirectional | SN74AVC4T245PW: 3.3V (MCU side) <-> 2.5V (FPGA side) |
| Power | +3.3V, +2.5V, GNDD | Input | 3.3V for MCU; 2.5V for translator FPGA side |

**Shall Statements:**

1. The MCU shall receive parameter update commands from the FPGA via SPI slave port 2.
2. The MCU shall configure all ADC ICs via SPI at power-on using the appropriate register map.
3. The MCU shall update digipot values on the HP/LP and parametric EQ subsystems within 1 ms of receiving a command from the FPGA.
4. The MCU shall control all relay bypass states via GPIO through the SPI fanout subsystem.
5. The MCU shall use the SN74AVC4T245PW level translator for all signals crossing the 3.3V/2.5V voltage domain boundary.
6. The MCU shall assert SPI2_SLAVE_BUSY when it cannot accept new commands, providing flow control to the FPGA.
7. The MCU shall not participate in the TDM audio data path.

### 7. SPI Fanout Subsystem

**Functional Description:** The SPI fanout subsystem distributes chip-select signals from the MCU's limited GPIO to the numerous SPI peripherals on the board (digipots, multiplexers, ADC ICs, relay drivers). The fanout uses shift registers driven by the MCU's SPI master port to generate an arbitrary number of active-low chip-select lines from a small number of MCU pins. The master chip-select line (CH1-4 MASTER CS) enables the fanout chain; individual output bits address specific peripherals.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| SPI In | Digital (SPI) | Input | SCK 1 O, MOSI 1 O from MCU |
| Master CS | Digital | Input | CH1-4 MASTER CS from MCU -- enables the fanout chain |
| Per-device CS Out | Digital | Output | Individual CS lines: CH1 HP DPOT CS, CH1 LP DPOT CS, CH1 Para1_F DPOT CS, CH1 Para1_G DPOT CS, CH1 HPLP decade CS, etc. |
| Relay CS | Digital | Output | Relay Master CS -- controls relay driver for filter bypass |
| HPLP Master CS | Digital | Output | HPLP Master CS -- group select for HP/LP digipot bank |
| Power | +3.3V, GNDD | Input | Digital supply |

**Shall Statements:**

1. The SPI fanout subsystem shall decode the MCU's SPI master output into individual chip-select lines for all SPI peripherals on the board.
2. The fanout shall support a minimum of 16 individually addressable chip-select outputs per channel group (4 channels on the eval board).
3. Only one chip-select output shall be active at any given time to prevent SPI bus contention.
4. The fanout shall operate at the MCU's SPI clock rate without introducing setup/hold violations on downstream peripherals.

### 8. Clock Generation Subsystem

**Functional Description:** The clock subsystem provides the master audio clock (MCLK) for the ADC and DAC ICs and serves as the timing reference for the entire audio data path. A 549CCAC000112ACG 10 MHz crystal oscillator [18] provides the on-board clock source. An external BNC input allows synchronization to an external word clock or master clock for multi-device setups. The FPGA's MMCM (Mixed-Mode Clock Manager) [13] generates all internal clocks (DSP, MicroBlaze, TDM bit clock) from this reference.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| Crystal Oscillator Out | Clock (10 MHz) | Output | 549CCAC000112ACG -> MCLK distribution |
| External Clock In | Clock | Input | BNC connector for external master clock / word clock |
| MCLK_AD | Clock | Output | Master clock to ADC subsystem |
| MCLK_DA | Clock | Output | Master clock to DAC subsystem |
| MCLK to FPGA | Clock | Output | Reference input for FPGA MMCM |
| Power | +3.3VA, GND1 | Input | Dedicated analog supply and ground for clock oscillator |

**Shall Statements:**

1. The clock subsystem shall provide a stable 10 MHz master clock from the on-board crystal oscillator (549CCAC000112ACG).
2. The clock subsystem shall provide an external clock input (BNC connector) for synchronization to an external master clock source.
3. The clock output shall have a dedicated analog power supply (+3.3VA) and separate analog ground (GND1) to minimize jitter from digital noise coupling.
4. The clock subsystem shall distribute MCLK to both the ADC and DAC subsystems with matched trace lengths (within 10% of each other).
5. Clock jitter shall be less than 100 ps RMS to meet professional audio performance requirements.

### 9. Power Supply Subsystem

**Functional Description:** The power supply subsystem converts the external 12V DC input into all voltage rails required by the system. For the evaluation board: AZ1117-3.3 provides the 3.3V digital rail, AZ1117-1.2 provides the 1.2V ADC core rail, and an LM317/LM337 pair generates the +/-15V split supply for analog opamp stages. Separate analog and digital ground planes are used to minimize noise coupling. The +2.5V rail for the FPGA I/O interface is provided by the AC701 evaluation board's on-board regulators.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| 12V DC In | Power | Input | External power supply, barrel jack or screw terminal |
| +3.3V Out | Power | Output | Digital logic, MCU, ADC digital, level translators |
| +1.2V Out | Power | Output | ADC core supply |
| +2.5V Out | Power | Output | ADC analog reference, FPGA I/O (eval board: from AC701) |
| +15V Out | Power | Output | Positive opamp supply (MC33079 stages) |
| -15V Out | Power | Output | Negative opamp supply (MC33079 stages) |
| GND / GNDD / GND1 | Power | -- | Separate digital, analog, and clock ground planes |

**Shall Statements:**

1. The power supply shall accept a 12V DC input and generate all required voltage rails.
2. The +3.3V rail (AZ1117-3.3) shall supply at least 1A for the MCU, ADC digital sections, and SPI peripherals.
3. The +1.2V rail (AZ1117-1.2) shall supply at least 500 mA for ADC core voltage.
4. The +/-15V rails (LM317/LM337) shall supply at least 200 mA each for the opamp stages.
5. The power supply shall maintain separate analog and digital ground planes, connected at a single star-ground point.
6. All voltage regulators shall include appropriate input/output decoupling capacitors per their respective datasheets.
7. The power supply shall include reverse-polarity protection on the 12V DC input.

### 10. FPGA / TDM Interface Subsystem

**Functional Description:** The FPGA subsystem is the central processing hub of the system. Implemented on the Xilinx Artix-7 XC7A200T on the AC701 evaluation board, it receives digitized audio from the ADC boards via TDM4 serial links, processes the audio through the DSP engine (biquad EQ, dynamics, mixing), and transmits the processed output to the DAC boards via TDM4. The FPGA also hosts the control plane (dual MicroBlaze processors [14]) that manages DSP coefficients, system configuration, and the Gigabit Ethernet host interface. The FPGA communicates with the on-board MCUs via SPI to relay filter parameter changes from the host PC.

**Interfaces:**

| Interface | Signal Type | Direction | Details |
|---|---|---|---|
| TDM4 RX (ADC -> FPGA) | Digital serial | Input | BCLK_AD, WS_AD, TDM IN 1, TDM IN 2 |
| TDM4 TX (FPGA -> DAC) | Digital serial | Output | BCLK_DA, WS_DA, TDM OUT 1 |
| SPI to MCU | Digital (SPI) | Output (master) | SCK 2, SDI2, SDO2, SPI2_SS -- commands to board MCU |
| MCLK Reference | Clock | Input | From clock subsystem, input to MMCM |
| Gigabit Ethernet | Digital | Bidirectional | RGMII to Marvell 88E1116R PHY on AC701 |
| DDR3 Memory | Digital | Bidirectional | On-board AC701 DDR3 (256 MB) for buffers and model storage |
| UART Debug | Digital serial | Output | Debug console via AC701 USB-UART |

**Shall Statements:**

1. The FPGA shall receive audio data from at least 2 TDM4 serial input links (8 channels on the eval board; 16 TDM4 links for the full 64-channel production system).
2. The FPGA shall transmit processed audio on at least 1 TDM4 serial output link (8 channels on the eval board).
3. The FPGA shall implement a per-channel DSP processing chain consisting of: 1 HPF biquad, 4 parametric EQ biquads, noise gate, compressor, gain stage, and delay -- all independently bypassable.
4. The FPGA shall implement a mixing engine capable of routing 64 input channels to 66 output buses.
5. The FPGA shall apply TPDF dither on all output channels before truncation to 24-bit DAC word width.
6. The FPGA shall complete all per-frame DSP processing within the audio frame period (20.83 us at 48 kHz) with deterministic latency.
7. The FPGA shall host a dual MicroBlaze control plane: RT_MB for real-time coefficient computation and frame scheduling, SYS_MB for Ethernet/OSC host communication and system management.
8. The FPGA shall communicate with the host PC via Gigabit Ethernet using the Open Sound Control (OSC) protocol for all parameter and configuration commands.
9. The FPGA shall communicate with on-board MCUs via SPI master to relay analog filter parameter changes from the host.
10. A SYS_MB crash shall not interrupt audio processing; the RT_MB shall continue operating independently with last-known-good coefficients.

## Ethical, Professional, and Standards Considerations

### Broader Implications, Ethics, and Responsibility as Engineers

Contributor: Zachary

On a broader scale, this project can enable more communities to have access to studio-grade recording systems such as churches or independent music studios. By offering a more affordable alternative, other companies may be forced to bring down their artificially high prices. Additionally, improved affordability could enable the growth of more independent studios which might allow niche musical artists and creative groups to attain more success.

The audio industry is also notoriously slow to adapt to change. This project with its inclusion of digitally controllable analog filters has the potential to push the technology into the mainstream. This might lead to additional innovation in the future, much like the change from 2" tape to digital recording formats. 

While there are very few potential negative impacts of this project, a major argument that almost always occurs from a reduction of the barrier-to-entry is degradation of the quality of music. It has been argued over the past 20-25 years that the quality of mainstream music has degraded due in part to the very low barrier-to-entry. Artists with a laptop and two-channel recorder in 2026 can make a record that is on many levels technically superior to a $300,000 record recorded in a multi-million dollar recording studio in 1996. There is likely very little that can be done about this, as the issue is a broader societal one, rather than one created by a lack of gatekeeping. 

### Professional Responsibility

As engineering professionals, the team has a responsibility to ensure the system is designed and documented to a standard that another qualified engineer could reproduce, verify, and maintain. All design decisions are traceable to specifications or constraints documented in this report. Safety-critical design choices -- particularly in the power supply and high-voltage analog sections -- are informed by IEC 62368-1 and are not left to manufacturing discretion. The team also acknowledges a responsibility to provide honest performance characterization; audio specifications (THD+N, dynamic range, crosstalk) shall be measured using AES17-compliant methods and reported without embellishment.

### Standards Compliance and Design Impact

The design is directly informed by the standards identified in the constraints section. IEC 62368-1 [1] influences the power supply design: all user-accessible voltages are below the hazardous energy thresholds defined by the standard, the 12V DC input uses a class-rated external supply, and the +/-15V analog rails are internal to the enclosure with no user-accessible contact points. CISPR 32 [2] Class B emission limits inform the PCB layout practices: separate analog and digital ground planes, controlled impedance for high-speed SPI and TDM traces, and ferrite beads on power rail entry points. The AES3 standard [3] directly defines the TDM4 framing protocol used for all audio data transfer. AES17 [4] measurement methods shall be used during system validation to verify THD+N, frequency response flatness, and interchannel crosstalk specifications.

RoHS compliance is enforced through component selection -- all parts in the BOM are verified RoHS compliant, and lead-free assembly processes are specified in the JLCPCB fabrication order.

## Resources

### Budget

The following budget covers the evaluation board prototype. Production board and FPGA motherboard budgets are estimated separately where applicable.

**ADC Subsystem (Eval Board -- 4 channels):**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| ES9842Q PRO (4-ch ADC) | 1 | $12.50 | $12.50 |
| ES9312Q (Dual LDO, precision ref) | 1 | $3.20 | $3.20 |
| Passive components (R, C, ferrites) | ~50 | ~$0.05 avg | $2.50 |
| Coaxial connectors (BNC) | 4 | $1.50 | $6.00 |
| 2x8 pin header (TDM connector) | 1 | $0.80 | $0.80 |
| **ADC Subtotal** | | | **$25.00** |

**DAC Subsystem (Eval Board -- 8 channels):**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| ES9017S (8-ch DAC) | 1 | $8.00 | $8.00 |
| MC33079 (Quad opamp, output stage) | 4 | $1.50 | $6.00 |
| Passive components (R, C) | ~60 | ~$0.05 avg | $3.00 |
| Coaxial connectors (BNC) | 8 | $1.50 | $12.00 |
| 2x16 pin header (TDM + power) | 1 | $1.20 | $1.20 |
| **DAC Subtotal** | | | **$30.20** |

**Analog Filter Subsystem (Eval Board -- 2 channels populated):**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| MCP41U83T-502E/ST (digipots) | 26 (13/ch x 2 ch) | $2.10 | $54.60 |
| MC33079 (Quad opamp, filter stages) | 8 | $1.50 | $12.00 |
| EC2-5NU (Signal relays) | 6 | $2.50 | $15.00 |
| 8:1 MUX ICs (decade switching) | 4 | $1.00 | $4.00 |
| Capacitor arrays (film, C0G) | ~40 | ~$0.15 avg | $6.00 |
| Passive components (R, C) | ~80 | ~$0.05 avg | $4.00 |
| **Analog Filter Subtotal** | | | **$95.60** |

**MCU Subsystem:**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| PIC32MM0256GPM064-E/MR | 1 | $4.50 | $4.50 |
| SN74AVC4T245PW (Level translator) | 2 | $1.20 | $2.40 |
| Passive components (decoupling, pull-ups) | ~15 | ~$0.05 avg | $0.75 |
| 1x5 pin header (ICSP) | 1 | $0.50 | $0.50 |
| **MCU Subtotal** | | | **$8.15** |

**Clock Subsystem:**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| 549CCAC000112ACG (10 MHz XO) | 1 | $3.50 | $3.50 |
| BNC connector (ext clock input) | 1 | $1.50 | $1.50 |
| Passive components (decoupling) | ~5 | ~$0.10 avg | $0.50 |
| **Clock Subtotal** | | | **$5.50** |

**Power Subsystem:**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| AZ1117-3.3 (3.3V LDO) | 1 | $0.50 | $0.50 |
| AZ1117-1.2 (1.2V LDO) | 1 | $0.50 | $0.50 |
| LM317DCYR (Adjustable +15V) | 1 | $0.65 | $0.65 |
| LM337 (Adjustable -15V) | 1 | $1.10 | $1.10 |
| Passive components (R, C, diodes) | ~25 | ~$0.05 avg | $1.25 |
| DC barrel jack | 1 | $0.80 | $0.80 |
| **Power Subtotal** | | | **$4.80** |

**SPI Fanout Subsystem:**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| Shift registers (74HC595 or equiv) | 4 | $0.50 | $2.00 |
| Passive components (decoupling) | ~10 | ~$0.05 avg | $0.50 |
| **Fanout Subtotal** | | | **$2.50** |

**PCB Fabrication (Eval Board):**

| Item | Qty | Unit Cost | Subtotal |
|---|---|---|---|
| JLCPCB fabrication (4-layer, 5 pcs) | 1 order | $30.00 | $30.00 |
| JLCPCB stencil | 1 | $10.00 | $10.00 |
| Solder paste, flux, misc consumables | -- | -- | $15.00 |
| **Fab Subtotal** | | | **$55.00** |

**Eval Board Total: ~$226.75**

**Production Scale Estimates:**

| Item | Estimated Cost |
|---|---|
| 4x 16-ch AD/DA production boards (components + fab) | ~$1,200 |
| FPGA Motherboard (stretch goal, components + fab) | ~$550 |
| AC701 Evaluation Board (already owned) | $0 |
| PICKIT 5 (already owned) | $0 |
| **Estimated Full System Total** | **~$1,975** |

**FPGA Motherboard Power Subsystem (Stretch Goal):**

The power subsystem budget is estimated to be around $55.

* 3 FS1412 DC/DC converters at $10.70 each  
* 1 FS1006 DC/DC converter at $5.06 each  
* 2 TPS51200 Power Management Specialized DDR Term Regulators at $1.64 each  
* 1 UCD90120A Supervisory Circuits 12-Rail Pwr Supply Sequencer & Mon at $11.22 each

### Division of Labor

The division of labor is based on each team member's skill set and the subsystem requirements:

| Team Member | Primary Responsibility | Secondary Responsibility |
|---|---|---|
| **Adam** | ADC board design (schematic, layout, component selection), DAC board design, analog filter circuit design (Harrison 32C adaptation), overall system integration | PCB fabrication coordination with JLCPCB, BOM management |
| **David** | FPGA HDL development (TDM interface, DSP engine, MicroBlaze control plane), Ethernet/OSC host interface, digital filter implementation, MCU firmware (SPI slave, digipot control) | Clock architecture, SPI fanout design, level translator integration, system-level timing verification |
| **Zachary** | Power supply design (eval board LDO/linear, production DC/DC), mechanical design (enclosure, standoffs, board mounting), component sourcing and procurement | PCB assembly assistance, testing and validation support |

All three team members shall participate in system integration testing and documentation.

### Timeline

The following Gantt chart outlines the project timeline across the two-semester capstone sequence. Critical path items are prioritized to maximize integration testing time.

```
Gantt Chart -- 64-Channel TDM Audio Interface
Fall 2025 - Spring 2026

                          Sep  Oct  Nov  Dec  Jan  Feb  Mar  Apr  May
                          ---- ---- ---- ---- ---- ---- ---- ---- ----
PHASE 1: DESIGN
  Conceptual Design Doc   ####
  Eval Board Schematic    #### ####
  Eval Board Layout            #### ##
  FPGA HDL (TDM core)    #### #### ####

PHASE 2: EVAL BOARD FAB
  JLCPCB Order                      ####
  Component Procurement             #### ##
  Eval Board Assembly                    ####
  MCU Firmware (SPI)           #### ####

PHASE 3: INTEGRATION
  ADC Bring-up/Test                      #### ####
  DAC Bring-up/Test                      #### ####
  Analog Filter Test                          #### ####
  FPGA TDM Integration                       #### ####
  Ethernet/OSC Bring-up                            #### ####

PHASE 4: PRODUCTION
  16-ch Board Schematic                            #### ####
  16-ch Board Layout                                    #### ##
  16-ch Board Fab + Assy                                     #### ####
  FPGA Motherboard (stretch)                                 #### ####

PHASE 5: VALIDATION
  Full System Integration                                         ####
  AES17 Audio Measurements                                        ####
  Final Documentation                                             ####
  Capstone Demo                                                      #

MILESTONES
  * Conceptual Design Due       [Sep]
  * Eval Board Ordered          [Nov]
  * Eval Board Audio Pass-thru  [Jan]
  * DSP Processing Verified     [Feb]
  * Production Boards Ordered   [Mar]
  * Full System Demo            [May]
```

**Critical Path:** Eval board schematic -> fabrication -> ADC/DAC bring-up -> FPGA TDM integration. The FPGA HDL development runs in parallel with board design so that the TDM interface is ready for testing as soon as the eval board is assembled. The production board design does not begin until the eval board has passed audio pass-through testing, ensuring that any design errors are corrected before scaling to 16 channels.

## References

[1] IEC 62368-1:2023, "Audio/video, information and communication technology equipment -- Safety requirements."

[2] CISPR 32:2015+AMD1:2019, "Electromagnetic compatibility of multimedia equipment -- Emission requirements."

[3] Audio Engineering Society, "AES3: AES Standard for Digital Audio -- Engineering Guidelines for the Multichannel Audio Digital Interface," 2009.

[4] Audio Engineering Society, "AES17: AES Standard Method for Digital Audio Engineering -- Measurement of Digital Audio Equipment," 2020.

[5] ESS Technology, "ES9842Q PRO Datasheet: SABRE PRO Hyperstream II Quad ADC," 2023.

[6] ESS Technology, "ES9017S Datasheet: SABRE 32-bit High-Performance 8-Channel DAC," 2022.

[7] ESS Technology, "ES9312Q Datasheet: SABRE Dual Channel Ultra Low Noise Linear Regulator," 2023.

[8] Microchip Technology, "PIC32MM0256GPM064 Datasheet: 32-bit Microcontrollers," 2023.

[9] Microchip Technology, "MCP41U83T Datasheet: 7/8-Bit Single SPI Digital POT with Volatile Memory," 2019.

[10] AMD (Xilinx), "UG952: AC701 Evaluation Board for the Artix-7 FPGA User Guide," v1.4, 2019.

[11] AMD (Xilinx), "DS181: Artix-7 FPGAs Data Sheet: DC and AC Switching Characteristics," v1.27, 2022.

[12] AMD (Xilinx), "UG479: 7 Series DSP48E1 Slice User Guide," 2023.

[13] AMD (Xilinx), "UG472: 7 Series FPGAs Clocking Resources User Guide," 2023.

[14] AMD (Xilinx), "PG150: MicroBlaze Processor Reference Guide," 2023.

[15] Robert Bristow-Johnson, "Audio EQ Cookbook," 2024. Available: https://www.w3.org/2011/audio/audio-eq-cookbook.html

[16] ON Semiconductor, "MC33079 Datasheet: Low Noise Quad Operational Amplifiers," 2019.

[17] Texas Instruments, "SN74AVC4T245 Datasheet: 4-Bit Dual-Supply Bus Transceiver," 2023.

[18] Abracon, "549CCAC000112ACG Datasheet: 10 MHz CMOS Oscillator," 2022.

[19] Diodes Incorporated, "AZ1117 Datasheet: 1A Low Dropout Linear Regulator," 2021.

[20] Texas Instruments, "LM317 Datasheet: 1.5A Adjustable Linear Regulator," 2023.

[21] Texas Instruments, "LM337 Datasheet: 3-Terminal Adjustable Negative Regulator," 2023.

[22] D. Zoelzer, *DAFX: Digital Audio Effects*, 2nd ed. Chichester, UK: Wiley, 2011.

[23] S. P. Lipshitz, R. A. Wannamaker, and J. Vanderkooy, "Quantization and Dither: A Theoretical Survey," *J. Audio Eng. Soc.*, vol. 40, no. 5, pp. 355-375, May 1992.

[24] A. Forencich, "verilog-ethernet: Verilog Ethernet Components," 2024. Available: https://github.com/alexforencich/verilog-ethernet

[25] S. Goldschmidt, "lwIP -- A Lightweight TCP/IP Stack," 2024. Available: https://savannah.nongnu.org/projects/lwip/

## Statement of Contributions

Each team member is required to document their individual contributions below. By submitting, the team certifies that each member's statement of contributions is accurate.

**Adam:**

**David:**

**Zachary:**
