# **64 Channel AD/DA Project Proposal**

## **Introduction**

Contributor: Adam

The system defined in this proposal at its most basic level is a 64 channel in, 64 channel out Analog to Digital/Digital to Analog Converter (AD/DA). On the AD side, analog signals coming from sources such as a microphone preamplifier or a keyboard, are digitized and sent to a PC running commercial recording software known as a Digital Audio Workstation (DAW). The DA side can take a digital signal from the PC, convert it to analog and send it to an external piece of analog equipment such as an equalizer or reverb unit. 

On top of this, the AD side will offer digitally controllable analog filters and DSP filters. This allows the user to benefit from the zero latency offered by the analog filters, and/or the extreme filter shapes offered by DSP. This all happens locally on the device, reducing the load on the computer’s processor, which would typically perform DSP from within the DAW. 

While 64x64 converters exist on the market now, they are extremely expensive (to the tune of $10,000 and up). There are smaller systems on the market which can be combined together, but these rely on external interface devices which increase cost and reduce reliability. This project aims to unify everything a user might need into one package, while also reducing cost. 

In the body of this proposal, further details will be discussed relating to the budget and scope of the project as well as why this project should exist in the first place. The team will also provide minimum requirements to deem the project a success, as well as options and workarounds for common issues that can be anticipated when developing a product from the ground-up. 

## **Formulating the Problem**

Adam, David

In this section, we consider the following questions:

* Who does the problem affect?  
  * Commercial Recording Studios  
  * Broadcasting Studios  
  * Independent Recording Artists  
  * Churches (1500+ members)  
* Why do we need this solution?  
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
* Why aren’t off-the-shelf solutions sufficient?  
  * None of the solutions on the market implement digitally controllable analog filters   
  * The few off the shelf solutions that do exist are extremely expensive and still require external interfaces  
    * Example: The Antelope Orion Galaxy 64 is $10,000, requires a Dante PCIe card ($1500). Antelope is also infamous for having **poor** software support  
    * Example: The RME M32 can be purchased in either AD or DA conversion. To achieve a 64x64 count, four units in total would need to be purchased at a cost of $4000 each, for a total cost of $16,000 

### **Background**

Contributor: Adam

The project device has three subsystems which have varying degrees of complexity. In order of complexity: FPGA motherboard, ADC board, and DAC board. Depending on budgetary restrictions, some of these systems may need to be reduced in scope. 

Since PCBway is the intended manufacturer of PCBs, the minimum order quantity is 5 boards. Therefore, the most cost-effective (and least wasteful) option is to design the project around four 16-channel PCBs for the ADC and DAC designs. This way, one minimum quantity order will yield enough boards to populate one full prototype unit, with a spare PCB leftover for the ADC and DAC each. 

The DAC boards will have a minimum amount of components on them, as they will consist of an MCU, two 8-channel DAC chips, a precision reference IC, and 16 quad opamps plus the associated R/L/C values. These boards can very likely all be built to completion. 

The ADC boards have a high number of peripheral components related to the analog filters. This will necessitate the inclusion of many shift registers, multiplexers and digipots. The chosen ADC chips also only come in 4-channel variants, so a single 16-channel board will require 4 ADC ICs. Therefore, depending on budgetary constraints, it may be necessary to populate less of the filters. In an extreme case, it may be necessary to also reduce the number of ADCs. A good balance is likely related to populating all 64 channels worth of ADCs, with perhaps 2-8 analog filters. 

The FPGA motherboard is a high-priority stretch goal. Since the team already owns an AC701 evaluation board, the project can be completed in its entirety only using the ADC, DAC and AC701 boards. However, the team has a large desire to manufacture an FPGA board, as it would be an extremely powerful career highlight for entering industry. This presents two potential options for accomplishing the motherboard design

* Go with the same FPGA on the evaluation board. This chip alone is $330, but yields the highest likelihood of success, considering the reference design from the AC701 can be used.  
* Go with a cheaper chip. Depending on the design requirements, it may be possible to use a less powerful chip. The only caveat to this approach is that there may be a higher risk of a catastrophic PCB design failure, since a chip-specific reference design may not exist. 

With the scenarios for each subsystem described, the team believes it is possible to achieve an end product that will give the most experience possible from this design sequence, leading to job positions beyond entry level after graduation. It is for this reason that the FPGA motherboard, while a stretch goal, should still be attempted.

### **Specifications and Constraints**

Contributors: Adam, David

#### **Specifications**

Specifications are requirements imposed by **stakeholders** to meet their needs. If a specification seems unattainable, it is necessary to discuss and negotiate with the stakeholders.

This section will likely have to be updated after the due date. The team is currently in talks with two companies who are interested in becoming a stakeholder, but the formal meeting to discuss this may not happen until after the due date.

#### 

#### **Constraints**

Contributors: David, Zachary 

Questions to consider:

* Do governing bodies regulate the solution in any way?  
* **Are there industrial standards that need to be considered and followed?**  
* What impact will the engineering, manufacturing, or final product have on public health, safety, and welfare?  
* Are there global, cultural, social, environmental, or economic factors that must be considered?

At the time of this project proposal, it is difficult to determine which governing standards may apply. Considering the device does not use any wireless communication, utilize high voltage, or interface with other systems capable of causing damage to people or buildings, the most likely application of organizational standards is in the project’s power supply. In all likelihood, the team will procure a power supply which will comply with the following standards found below. However, depending on the power requirements necessitated by the project device, this cannot be guaranteed by the due date of this proposal. 

This project is designed with the intention to comply with the standards and codes set by the United States government and standards organizations regarding safety and industry standards. The following standards have been listed below which may be applicable to this project: 

The following standards relate to devices pertaining to audio and frequencies:

* IEC 62368-1   
  *  This standard sets the requirements for telecommunication devices. This standard includes the safety of electronic equipment including audio technology and equipment, amplifiers, PCs, and computers and their power supplies.


* CISPR 32  
  * The CISPR 32 is the international EMC standard for electromagnetic emission requirements of multimedia equipment (MME) with an R.M.S. voltage not exceeding 600v

## 

## 

## 

## **Survey of Existing Solutions**

Contributor: Adam

This project combines elements of two discrete product types on the market. The first, and most unique element of the project are the digitally controllable analog filters.

**Digitally controlled analog filters**

Each channel on the ADC side will have access to a filter chain consisting of high pass, low pass, and three parametric equalizers capable of 12dB of boost/cut. Digital control of analog circuits in the professional audio world are very rare due to the cost of parts and the added complexity. 

Historically, the first device capable of this type of automation was the Harrison Series 10 mixing console, released in 1985\. \[4\]

Decades later, only three mainstream devices capable of digital control of an analog circuit exist:  

* The WesAudio Hyperion, offering 4 bands of parametric equalization \[1\]  
* The Fredenstein F610 EU-1500, offering 4 bands of parametric equalization as well as a low-cut \[2\]  
* Bettermaker Mastering Equalizer, offering 4 bands of parametric equalization \[3\]

Between the three, the Fredenstein and Bettermaker offer digital control, but each filter band is limited to bandwidth traversal (eg: filter1 only traverses from 500Hz-2.5kHz). The Fredenstein offers a 20Hz-20kHz traversal on all 4 filters, though only 20Hz, to 975 Hz on the low cut (high pass). 

**AD/DA converters**

The main players in the 64x64 AD/DA market are the companies Prism Sound and Antelope Audio. RME is another company that makes devices capable of 64x64 conversion, though multiple devices must be connected together to achieve this channel count. None of the devices covered in this section offer any kind of front-end analog signal processing. 

Both Prism and Antelope’s devices offer 64x64 conversion over Dante (proprietary ethernet standard) or MADI (optical, proprietary). In order to use these devices, either an external MADI interface is needed, or a PCIe card is needed for Dante.

Antelope’s Galaxy AD/DA is the closest fit to the project device. However, Antelope is notorious for poor software support. While it offers DSP similar to the project device, the licensing and always-on connection headaches make long-term support dubious. 

RME is well-known in the commercial recording industry, as their converters offer excellent specs and their software is known to be rock-solid. The M-32 series come in either AD only or DA only. The can be connected to a host PC via ethernet on a local area network connection. This by far is the least proprietary and most accessible option. The main drawback is the cost. The M32 series is the highest channel offering by RME, and as previously mentioned *only* exist in either AD or DA format. This means that four units in total are needed. At a cost of $4000 each, a 64x64 RME system is $16,000. 

## **Measures of Success**

Contributors: Adam, David, Zachary

Define how the project’s success will be measured. This involves explaining the experiments and methodologies to verify that the system meets its specifications and constraints.

This project has several major tasks associated with it. The bold sections below are the various subsystems involved in the completion of the project. The bullet points in those sections are the minimum requirements to call each subsystem a success. 

**ADC circuit board design**

	To call the ADC design a success, the following tasks shall be completed

* Design of the digitally controllable analog filters  
* Simulation and subsequent breadboard testing of the filters  
* Interfacing the breadboard analog filters with a breadboard ADC chip  
* Communication from the successfully breadboarded circuit to the FPGA Evaluation board  
* PCB layout and ordering   
* Building the delivered PCBs (soldering and heat reflow)  
* Testing the completed PCBs and verification of data delivered to FPGA Evaluation board

Due to budget constraints, it may only be possible to populate only a few of the digitally controllable analog filters. Therefore, to prove control over all 64 channels, only two fully populated channels will be sufficient to demonstrate this feature. 

**DAC circuit board design**

	To call the DAC design a success, the following tasks shall be completed

* Design of DAC Analog output stage  
* Simulation of the DAC output stage  
* Breadboarding of the DAC and the analog output stage   
* Communication between FPGA and DAC resulting in a signal that can be listened to  
* PCB layout and ordering  
* Building the delivered PCBs (soldering and heat reflow)  
* Testing the completed PCBs, and satisfactory aural evaluation of the converted digital datastream to analog waveform. 

**FPGA motherboard design (stretch goal)**

	Due to the associated complexity of a purpose-built FPGA motherboard design, this is designated as an optional stretch goal. The project is capable of working in its entirety between the AC701 evaluation board and the ADC/DAC boards. 

To call the FPGA motherboard design a success, the following tasks may be completed

* Circuit design of the FPGA motherboard  
* PCB layout and ordering  
* Building the delivered PCB (soldering and heat reflow)  
* Evaluating the completed PCB against a test routine capable of testing all the combined subsystems  
* 1:1 replication of the tasks performed by the AC701 FPGA evaluation board

Due to the tight budget constraints of the project, the following list is a minimum amount of tasks which need to be completed for the project to be considered a success:

* ADC board shall digitally control all parameters of at least two channel’s worth of analog filters  
* FPGA Evaluation board shall transmit valid data via ethernet to a host PC  
* FPGA Evaluation board shall control parameters on ADC/DAC boards  
* An analog signal shall be filtered on the ADC board, captured by the ADC, processed on the FPGA evaluation board using DSP, converted back to analog, and sent out via the DAC board to speakers for aural evaluation.  
* FPGA Eval board shall encrypt sensitive user data with SHA-256 or similar  
* The system shall comply with CISPR 32

## **Resources**.

### **Software/Web:** 

* KiCAd (Possibly switching over to Altium) (PCB design)   
* Vivado/Vitis (FPGA development)   
* MPLABX (Embedded development)   
* Multisim Online (Circuit simulation)  
* PCBway (Circuitboard manufacturing)  
* Mouser (Parts ordering)  
* Digikey (Parts ordering)  
* MATLAB (simulation)  
* Visual Studio (PC application development)

### 

### **Hardware:**

* DSlogic (Logic Analyzer)  
* AMD Artix AC701 FPGA evaluation board  
* Rigol DS1054Z Oscilloscope (100MHz)  
* SAA2 Vector Network Analyzer  
* Digital multimeter  
* Proster BM4070 LCR tester  
* Microchip PICKIT 5 in-circuit debugger  
* Programmable bench power supplies (numerous)  
* Electronic Hotplate (for PCB reflow)  
* 862BD+ Solder/Heat gun station  
* Antelope Orion 32+ (reference design)   
* Access to TTU X-ray (to X-ray PCB)  
* TD8620 TeslaMeter  
* Access to Capstone Lab (primarily for accessing 1GHz oscilloscope)

### 

### **Budget**

Contributors: Adam, David

Provide a budget proposal with justifications for expenses such as software, equipment, components, testing machinery, and prototyping costs. This should be an estimate, not a detailed bill of materials.

* Power supply and distribution (including regulators and PMBus controllers) \- $250  
* 2u 19” rack chassis enclosure \- $75  
* PCBs (2x 4-layer, 1x 8-layer) \- $450  
* Analog to Digital board components $500  
* Digital to Analog board components $75  
* FPGA board components $550  
  Total estimated cost: $2000

### **Personnel**

Contributors: Adam, David, Zachary

The members of Team 2 have skills ranging from PCB layout, 3D modeling, programming, signal processing and synchronous/asynchronous digital communication, cost analysis and small-production assembly.. Below is a list of the individual skills for each team member: 

* Adam Light: C/C\#, Assembly, Multisim, KiCAD, Soldering, Cost Analysis, Hardware design  
* David Leathers: C/C++, Python, VHDL, Kiel, STM Cube, Soldering   
* Zachary Hirtz: C/C++, Soldering, Excel, Technical Writing,  

Below are the skills the team anticipates are needed to complete the project

* KiCAD/PCB design and layout  
* PCB reflow   
* HDL (Vivado IP blocks and System Verilog)  
* USB 3.1 protocol  
* Ethernet protocol  
* Serial communication standards: TDM, SPI, I2C   
* PCIe gen 2 

**Owen O’Conner** was chosen as the team supervisor, because of his interest in FPGAs. The previous experience of the team members centers around all of the components *external* to the FPGA platform. Mr O’Conner’s expertise in the field will help to guide the team in developing skills related to FPGA development. 

**Storm Johnson** is the Capstone instructor. Storm’s expertise as a project manager will be used to verify that tasks have been properly delegated, and that deadlines are within reach. Storm will also be used as a technical reference if the team is unable to solve certain problems. 

### **Timeline**

Contributors: Adam, David, Zachary
<img width="1323" height="595" alt="image" src="https://github.com/user-attachments/assets/b4d817bf-5ff2-4c71-93e4-21d59fb2c471" />

## 

## **Specific Implications**

Contributor: Zachary

What this project provides to the end customer is major cost-reduction, a unified all-in-one system dependent on zero peripherals and independence from any proprietary communication standard. Additionally, this project does not require digital rights management (DRM), or always-on authentication and therefore can be operated indefinitely without an internet connection. 

**Broader Implications, Ethics, and Responsibility as Engineers**

Contributor: Zachary

On a broader scale, this project can enable more communities to have access to studio-grade recording systems such as churches or independent music studios. By offering a more affordable alternative, other companies may be forced to bring down their artificially high prices. Additionally, improved affordability could enable the growth of more independent studios which might allow niche musical artists and creative groups to attain more success.

The audio industry is also notoriously slow to adapt to change. This project with its inclusion of digitally controllable analog filters has the potential to push the technology into the mainstream. This might lead to additional innovation in the future, much like the change from 2” tape to digital recording formats. 

While there are very few potential negative impacts of this project, a major argument that almost always occurs from a reduction of the barrier-to-entry is degradation of the quality of music. It has been argued over the past 20-25 years that the quality of mainstream music has degraded due in part to the very low barrier-to-entry. Artists with a laptop and two-channel recorder in 2026 can make a record that is on many levels technically superior to a $300,000 record recorded in a multi-million dollar recording studio in 1996\. There is likely very little that can be done about this, as the issue is a broader societal one, rather than one created by a lack of gatekeeping. 

## 

## 

## **References**

Contributors: Adam,David, Zachary

| \[1\] | Wes Audio, "WesAudio\_hyperion\_manual.pdf," \[Online\]. Available: https://wesaudio.com/uploads/manuals/WesAudio\_hyperion\_manual.pdf. \[Accessed 19 2 2026\]. |
| :---- | :---- |
| \[2\] | Fredenstein Professional Audio, "F610-Manual\_V1.1-print.pdf," \[Online\]. Available: https://www.fredenstein.com/wp-content/uploads/2022/10/F610-Manual\_V1.1-print.pdf. \[Accessed 19 2 2026\]. |
| \[3\] | Bettermaker , "Mastering Equalizer," \[Online\]. Available: https://www.bettermaker.com/mastering-equalizer. \[Accessed 19 2 2026\]. |
| \[4\] | Vintage King, "The History of Harrison Consoles- Vintage King," \[Online\]. Available: https://vintageking.com/blog/harrison-console-history/?srsltid=AfmBOopoZcU76kVHS5A6kVwFbrMuZ0wWwUfpQCtmZCzpSLTA3VljaRqs. \[Accessed 19 2 2026\]. |

## **Statement of Contributions**

Each section is labeled with the team member(s) associated with completion of that section. 

