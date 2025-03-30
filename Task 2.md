UART Loopback Mechanism Documentation
Objective:
Implement a UART loopback mechanism where transmitted data is immediately received back, facilitating testing of UART functionality on the VSDSquadron FPGA Mini board.

1. Study the Existing Code
The project involves configuring the UART interface on the FPGA to create a loopback. This means the data transmitted from the FPGA's TX pin will be immediately received back on the RX pin, helping to verify the UART communication system. Here's an overview of the Verilog code that facilitates this:

Module Overview: The Verilog code instantiates a UART Transmitter and Receiver. The TX pin is internally connected to the RX pin to create a loopback for testing. This ensures any data sent through the TX pin is immediately received back on the RX pin for verification.

Key Components:

UART Transmitter: Sends the data over the UART TX line.

UART Receiver: Receives data from the UART RX line.

FIFO Buffers: Temporarily hold the transmitted and received data.

Loopback Logic: The TX pin is connected to the RX pin within the FPGA for loopback functionality.

2. Design Documentation
Block Diagram:
The block diagram below illustrates the main components involved in the UART loopback system:

lua
Copy
  +------------------+         +-------------------+         +-----------------+
  |                  |         |                   |         |                 |
  | UART Transmitter |-------->| FPGA (Controller) |<------->| UART Receiver   |
  |                  |         |                   |         |                 |
  +------------------+         +-------------------+         +-----------------+
                    TX ↔ RX loopback
FPGA (Controller): The FPGA logic that controls both the transmitter and receiver.

UART Transmitter: Module responsible for sending data via the TX pin.

UART Receiver: Module that receives the data through the RX pin.

TX ↔ RX Loopback: The loopback is created by internally connecting the TX pin to the RX pin.

Circuit Diagram:
The circuit diagram shows the hardware setup, highlighting the loopback connection between the TX and RX pins of the FPGA:

pgsql
Copy
   +------------------+              +-------------------+
   | FPGA             |              | Serial Terminal   |
   |                  |              |                   |
   |    TX <-----> RX | <-----------> |                   |
   |                  |              |                   |
   +------------------+              +-------------------+
TX and RX Pin Connection: The TX pin from the FPGA is connected to the RX pin internally (or through external hardware, depending on the setup).

3. Implementation
Set Up the Hardware:
FPGA Connection: Connect the VSDSquadron FPGA Mini to your computer using the USB-C cable.

TX and RX Loopback: Ensure that the TX and RX pins on the FPGA are connected correctly, either internally in the FPGA or through an external UART interface.

If using an external UART-to-USB adapter, connect the TX pin of the FPGA to the RX pin of the adapter, and the RX pin of the FPGA to the TX pin of the adapter.

Serial Terminal: Set up a serial terminal on your computer (e.g., PuTTY, Tera Term, or screen) to communicate with the FPGA.

Ensure the serial terminal settings (baud rate, data bits, stop bits, and parity) match the Verilog code configuration.

Synthesize and Upload the Verilog Code:
Compile the Verilog code using the provided Makefile in the project:

Run make clean to clear any previous builds.

Run make build to compile the design.

Run sudo make flash to upload the compiled design to the FPGA.

Ensure that the FPGA is programmed correctly and is ready for UART communication.

4. Testing and Verification
Use a Serial Terminal:
Configure Serial Terminal:

Open a serial terminal (e.g., PuTTY, Tera Term).

Set the correct baud rate (e.g., 9600 baud), data bits (8), stop bits (1), and parity (none) as defined in the Verilog code.

Send Data:

Send a test string (e.g., "Hello, World!") from the serial terminal to the FPGA.

The FPGA should immediately receive the same data and send it back to the serial terminal via the loopback mechanism.

Verify Loopback:
If the loopback mechanism is working correctly, the serial terminal will display the same data that was sent.

For example, if you send "Hello, World!" from the terminal, the terminal should display it again immediately, confirming that the transmitted data was received back.

5. Documentation
Compile the Final Report:
The final documentation should include the following:

Block Diagram: Showing the architecture of the UART loopback system.

Circuit Diagram: Displaying how the TX and RX pins are connected and how the FPGA communicates with the serial terminal.

Explanation of Verilog Code:

Describe the UART Transmitter and Receiver modules.

Explain the FIFO buffer mechanism and how data is transferred between the TX and RX pins.

Describe the loopback mechanism that connects TX to RX internally.

Testing Results: Document the test procedure and provide screenshots or logs from the serial terminal showing data being received back after transmission.

Video Demonstration:
Record a short video demonstration showcasing the following:

Connect the FPGA to the computer and open the serial terminal.

Send data from the serial terminal to the FPGA.

Verify that the data is received back in the terminal, confirming the loopback functionality.

Final Deliverables:
GitHub Repository containing:

The Verilog code.

The block diagram and circuit diagram.

The final documentation (as described above).

Video Demonstration showing the UART loopback functionality in action.

Testing Logs from the serial terminal, including screenshots or logs of successful loopback communication.

This completes the documentation for the UART loopback mechanism on the VSDSquadron FPGA Mini.



