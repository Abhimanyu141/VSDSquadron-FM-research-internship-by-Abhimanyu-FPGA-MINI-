# VSDSquadron-FM-research-internship-by-Abhimanyu-FPGA-MINI-
VSDSquadron FPGA Mini (FM) Internship
🙌 Acknowledgement
Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd.
Professor Madhav Roa, International Institute of Information Technology, Bangalore
Professor Subir K Roy, International Institute of Information Technology, Bangalore
Professor Himanshu Rai, International Institute of Information Technology, Bangalore
Mr. Sudarshan R, National Institute of Advanced Studies (NIAS)
📚 Resources
VSDSquadron FPGA Mini (FM)

⚡ About FPGA Board
FPGA Mini FPGA Mini

The VSDSquadron FPGA Mini (FM) is a compact and low-cost development board designed for FPGA prototyping and embedded system projects. This board provides a seamless hardware development experience with an integrated programmer, versatile GPIO access, and onboard memory, making it ideal for students, hobbyists, and developers exploring FPGA-based designs.

The VSDSquadron FM board - Features and specifications:

� FPGA:
– Powered by the Lattice UltraPlus ICE40UP5K FPGA

– Offers 5.3K LUTs, 1Mb SPRAM, 120Kb DPRAM, and 8 multipliers for versatile design capabilities

� Connectivity:
– Equipped with an FTDI FT232H USB to SPI device for seamless communication

– All FTDI pins are accessible through test points for easy debugging and customization

� General Purpose I/O (GPIO):
– All 32 FPGA GPIOs brought out for easy prototyping and interfacing

� Memory:
– Integrated 4MB SPI flash for data storage and configuration

� LED Indicators:
– RGB LED included for status indication or user-defined functionality

� Form Factor:
– Compact design with all pins accessible, perfect for fast prototyping and embedded applications

Block Diagram

image

Board Overview

image

ICE40UP5K-SG48ITR Specification
Feature	Specification
Technology Node	40 nm
Logic Cells	5,280
Flip-Flops	4,960
SRAM Blocks	120 kbits
DSP Blocks	None
Package Type	SG48
I/O Pins	39
I/O Standards Supported	LVCMOS, LVDS
Max Operating Frequency	133 MHz
Clock Sources	Internal oscillator & external clock
Core Voltage	1.2V
I/O Voltage	3.3V, 2.5V, 1.8V
Operating Temperature	-40°C to 85°C
Development Tools	Project Icestorm, Yosys, NextPNR
UART Transmitter Module for FPGA - Overview
Project Summary
The UART Transmitter Module for FPGA is designed to enable serial communication between an FPGA and an external UART-compatible device (e.g., a PC, microcontroller, or another FPGA). This module will allow the FPGA to send data over a single wire (TX) to the external device using the Universal Asynchronous Receiver-Transmitter (UART) protocol. The goal of this project is to develop a fully functioning UART transmitter, integrate it into the FPGA design, and verify the module's performance through practical testing.

Key Tasks and Objectives
1. Study the Existing Code
Access the existing uart_tx project from the VSDSquadron_FM repository.

Review the Verilog code to understand the design and transmission process.

2. Design Documentation
Create a block diagram of the UART transmitter module, outlining key components such as the shift register, state machine, and baud rate generator.

Design a circuit diagram illustrating how the FPGA's TX pin connects to the receiving device (e.g., PC or microcontroller).

3. Implementation
Assemble the hardware setup as per the circuit diagram, ensuring proper connections between the FPGA and external device.

Synthesize the Verilog code and program the FPGA with the UART transmitter module.

4. Testing and Verification
Connect the FPGA to a UART-compatible device and use a serial terminal to monitor the transmitted data.

Verify that data is transmitted correctly, with accurate start bits, data bits, and stop bits.

5. Documentation
Document the project with detailed explanations of the block and circuit diagrams, a walkthrough of the Verilog code, and testing results.

Produce a short video demonstration showing the UART transmission in action.

Key Deliverables
Verilog Code: The complete code for the UART transmitter module.

Block Diagram: A visual representation of the system design.

Circuit Diagram: A diagram detailing the FPGA TX pin connection to the receiving device.

Testing Documentation: The results of the data transmission test, including any issues encountered and solutions implemented.

Video Demonstration: A short video showing the FPGA transmitting data to the receiving device, with the data verified in a serial terminal.

Objectives
The overall objective of this project is to design a UART transmitter on FPGA that reliably communicates with an external device. By following the steps of studying existing code, designing the system, implementing it on FPGA, and verifying the functionality, this project aims to provide a solid foundation for further UART communication developments on FPGA platforms.

UART Transmitter Module - Design Documentation
Project Overview
This documentation provides an overview of the design phase for the UART Transmitter Module for FPGA. The goal of this task is to define and document the design of the UART transmitter, which is responsible for sending serial data from the FPGA to an external UART-compatible device. This section includes the block diagram and circuit diagram that illustrate how the UART transmitter module is structured and how it interfaces with the external device.

Key Components
1. Block Diagram
The block diagram below shows the high-level architecture of the UART transmitter module. This diagram outlines the flow of data from input to transmission and describes the major components that make up the system.

Block Diagram:
plaintext
Copy
           +------------------+      +-------------------+  
           |   Data Input     |----->| Shift Register    |  
           +------------------+      +-------------------+  
                                            |  
                                            v  
                              +------------------------+  
                              | Baud Rate Generator    |  
                              +------------------------+  
                                            |  
                                            v  
                              +------------------------+  
                              |    State Machine        |  
                              +------------------------+  
                                            |  
                                            v  
                              +------------------------+  
                              |        TX Pin           |  
                              +------------------------+  
Components Breakdown:
Data Input:

The source of the data to be transmitted. This could be a data register or an input from another device (e.g., UART receiver or microcontroller).

Shift Register:

A shift register is used to convert the parallel data into a serial stream for transmission. Each bit is shifted out one at a time, starting from the least significant bit (LSB).

Baud Rate Generator:

This component generates the clock signal for transmitting data at the required baud rate. The baud rate generator divides the system clock to match the desired transmission rate (e.g., 9600 bps, 115200 bps).

State Machine:

The state machine manages the sequence of events during transmission. It ensures that the data is transmitted in the correct order with start, data, and stop bits:

Start Bit: Signals the beginning of data transmission (low).

Data Bits: The actual data being sent.

Stop Bit: Signals the end of data transmission (high).

TX Pin:

This is the FPGA’s output pin used for transmitting the serial data. The TX pin outputs the serialized data stream as per the state machine’s timing.

2. Circuit Diagram
The circuit diagram outlines how the FPGA's UART TX pin connects to the receiving UART-compatible device, such as a PC, microcontroller, or another FPGA.

Circuit Diagram:
plaintext
Copy
+--------------------+      +-----------------------+  
| FPGA TX Pin (GPIO) |----->| UART Receiver (RX Pin) |  
+--------------------+      +-----------------------+  
      |                             |  
      |                             |  
      |                           GND  
      |  
   VCC (3.3V or 5V, depending on FPGA)
Key Connections:
TX Pin (FPGA):

This is the output pin from the FPGA that transmits data. It is connected to the RX pin of the external receiving device.

RX Pin (Receiver):

The receiving device’s input pin, which will receive the serialized data sent from the FPGA.

Ground (GND):

A common ground connection is necessary to ensure both the FPGA and the receiving device operate with a shared reference voltage.

VCC:

The voltage supply (either 3.3V or 5V, depending on the FPGA and the receiving device). If the FPGA and the receiving device use different voltage levels, a level shifter may be needed to match the voltage levels of the TX and RX lines.

Design Considerations
1. Transmission Timing and Baud Rate
The baud rate must be correctly configured to match the transmission rate between the FPGA and the receiving device (e.g., 9600 bps, 115200 bps). The baud rate generator uses a clock divider to generate the necessary timing.

2. State Machine Design
The state machine ensures proper sequencing of the start bit, data bits, and stop bit. It needs to be robust and reliable to avoid transmission errors, such as misaligned data.

3. Shift Register for Serializing Data
The shift register converts parallel data into serial form for transmission. It works by shifting bits from the least significant bit (LSB) to the most significant bit (MSB) before sending them over the TX pin.

4. Signal Integrity
Proper care should be taken in the design of the PCB or the wiring between the FPGA and the receiving device to avoid signal degradation or noise, especially at higher baud rates.

Conclusion
The UART transmitter module consists of key components: a data input, shift register, baud rate generator, state machine, and TX pin. The block and circuit diagrams provide a high-level understanding of the design and how the FPGA interfaces with the receiving device. This modular design ensures that data is transmitted reliably using the UART protocol, and the system is flexible enough to work with different external devices.



