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
<details>
  <summary><STRONG> Task 1</STRONG></summary>

  # VSDSquadron-FM-research-internship-by-Abhimanyu-FPGA-MINI-
The VSDSquadron FPGA Mini (FM) is a compact and low-cost development board designed for FPGA prototyping and embedded system projects. This board provides a seamless hardware development experience with an integrated programmer, versatile GPIO access, and onboard memory, making it ideal for students, hobbyists, and developers exploring FPGA-based designs. [(Source)](https://www.vlsisystemdesign.com/vsdsquadronfm/)
## Task 1 Understanding and Implementing the Verilog Code on the VSDSquadron FPGA Mini Board
### Step 1: Understanding the Verilog code
The Verilog code can be accessed from here [Verilog code](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%201/VSDFM_top_module.v) This Verilog module controls an RGB LED using an internal oscillator and a counter, with a test signal for monitoring and predefined brightness settings for the LEDs.

<details>
  <summary><STRONG> Verilog module overview</STRONG></summary>

### Port Analysis:

```verilog
module top (
    // outputs
    output wire led_red,   // Red
    output wire led_blue,  // Blue
    output wire led_green, // Green
    input wire hw_clk,     // Hardware Oscillator, not the internal oscillator
    output wire testwire
);
```
**This is the first part of the code which tells about the ports:**

`led_red`, `led_blue`, `led_green` **(Outputs):** These ports are intended to control the red, blue, and green components of an RGB LED, respectively. By driving these outputs high or low, the module can manipulate the color and intensity of the LED.

`hw_clk` **(Input):** This is the hardware oscillator clock input. Although the module utilizes an internal oscillator `(int_osc)` for its operations, it has the clock signal which drives the module signals.

`testwire` **(Output):** This port is connected to the 5 bit of the `frequency_counter_i` register `(frequency_counter_i[5])`. It serves as a test signal, potentially useful for debugging or monitoring the internal state of the frequency counter.

### Internal Component Analysis
The module consists of three main internal components, each serving a distinct function:

#### 1. Internal Oscillator (SB_HFOSC)
The internal oscillator generates a stable clock signal required for timing operations. It is configured with a clock division value of `0b10`, which corresponds to binary 2.

**Power and Enable Signals:**

`CLKHFPU = 1'b1`: Powers up the oscillator.
`CLKHFEN = 1'b1`: Enables the oscillator.

**Output Signal:**

`CLKHF`: This is the oscillator's output, connected to the internal signal `int_osc`, which drives the frequency counter and other timing-dependent operations.
#### 2. Frequency Counter Logic
This module includes a **28-bit counter**, named `frequency_counter_i`, which increments on every rising edge of `int_osc`.

**Functionality:**
- The counter continuously increases its value, providing a timing reference within the module.
- Bit 5 of this counter is specifically connected to `testwire`, allowing external monitoring of the frequency.
- This setup helps verify the oscillator's operation and timing accuracy.

#### 3. RGB LED Driver (SB_RGBA_DRV)
The module includes an RGB LED driver that controls the brightness and color of the LED.

**Configuration and Control:**

- `RGBLEDEN = 1'b1`: Enables the LED operation.
- `CURREN = 1'b1`: Enables current control for LED brightness.

**Color Output Settings:**

- **Red LED** (`RGB0`)**:** Set to minimum brightness (RGB0PWM = 1'b0).

- **Green LED** (`RGB1`)**:** Set to minimum brightness (RGB1PWM = 1'b0).

- **Blue LED** (`RGB2`)**:** Set to maximum brightness (RGB2PWM = 1'b1).

**Current Settings:**

- Each LED is configured with minimal current (`0b000001`) to optimize power consumption.

### Purpose 
This Verilog module is designed to control an RGB LED while also handling internal timing functions. It includes a stable built-in clock and ensures smooth LED operation. Additionally, it features a test signal that allows monitoring of system behavior. The module is ideal for embedded applications that require precise LED control without relying on external timing components.

### Description of internal logic and oscillator
The module generates its own clock signal using a **high-frequency oscillator** (`SB_HFOSC`). This oscillator serves as the timing source for the entire system. A **28-bit counter** is connected to the oscillator’s output, which helps keep track of time and internal processes.

To assist with debugging and monitoring, **bit 5** of this counter is linked to the `testwire` output. This connection allows external systems to observe and verify the clock’s operation.

### Functionality of the RGB LED driver and its relationship to the outputs
The **RGB LED driver** (`SB_RGBA_DRV`) is responsible for managing the brightness and color of the LED. It operates with the following settings:

- Uses a **current-controlled** output to regulate brightness efficiently.
- Each LED color (Red, Green, Blue) is controlled via **Pulse Width Modulation (PWM)**.
- **Predefined brightness levels:**
            - **Blue LED** is set to **maximum brightness** (`RGB2PWM = 1'b1`).
            - **Red and Green LEDs** are set to **minimum brightness** (`RGB0PWM = 1'b0`, `RGB1PWM = 1'b0`).

In short, **This Verilog module controls an RGB LED using an internal oscillator and a frequency counter while providing a test signal for monitoring.**
</details>

### Step 2: Creating the PCF File

The PCF (Physical Constraints File) can be acessed here [(PCF)](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%201/VsdFpgaMini.pcf). A PCF (Physical Constraints File) is used in FPGA design to define the mapping between logical signals in the HDL design and the physical pins of the FPGA device. It ensures that specific signals, such as clock inputs, LED outputs, or communication interfaces, are correctly assigned to the appropriate hardware pins. The PCF file consists of simple constraints using the `set_io1` command, associating signal names with pin numbers. This file plays a crucial role in ensuring proper hardware functionality, as incorrect assignments can lead to design failures or unexpected behavior

<details>
  <summary><STRONG> PCF analysis</STRONG></summary>
We can easily create a Physical Constraints File (PCF). We can create the Physical Constraints File (PCF) for the FPGA project using the 
  
[Datasheet](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/VSDSquadronFMDatasheet.pdf) Here's how:
  
1. Identify I/O Ports in Your Verilog Module: Examine your Verilog code to list all input and output ports that need to be mapped to physical pins.

2. Consult the VSDSquadronFM Datasheet: The datasheet provides detailed information about the board's pinout and functionalities. Locate the section detailing the FPGA's pin assignments to understand which physical pins correspond to specific functions.
   ![image](https://github.com/user-attachments/assets/47e8710a-fb94-42d5-94e4-c63b50327710)
   
See this image of the Datasheet which tells about the pin assignments

4. Create the PCF File: Using the information from the datasheet, map each Verilog I/O port to the appropriate physical pin. For example, if your Verilog module has an output led_red and the datasheet indicates that the red LED is connected to pin 39, your PCF entry would be:
```
set_io  led_red	39
```
So for this project we have created the [(PCF)](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%201/VsdFpgaMini.pcf).

![image](https://github.com/user-attachments/assets/6ec66888-4189-416a-a14e-0b1065d70a05)

#### More detailed explanation of each set_io command in your Physical Constraints File (PCF):

##### Understanding set_io Commands in PCF

The set_io command is used in FPGA development to map logical signals (used in HDL code) to specific physical pins on the FPGA. This ensures that inputs and outputs in your Verilog/VHDL code are correctly connected to the corresponding hardware pins.

##### Breakdown of Each set_io Command
1. `set_io led_red 39`
**Purpose:** This command assigns the logical signal `led_red` to pin 39 on the FPGA.
**Functionality:** When your HDL code sets `led_red` to HIGH (1), it will turn on the red LED connected to pin 39. Similarly, setting it to LOW (0) will turn it off.
Use Case: Typically used for LED status indicators (e.g., power, error, activity).

2. `set_io led_blue 40`
**Purpose:**  Maps the `led_blue` signal to pin 40.
**Functionality:** This allows the FPGA to control a blue LED, which will turn on/off based on the signal from the Verilog/VHDL code.
Use Case: Used for visual indicators, such as showing different states of the system.

3. `set_io led_green 41`
**Purpose:** Assigns the `led_green` signal to pin 41.
**Functionality:** The FPGA can now control a green LED, switching it on or off as needed.
Use Case: Often used for status LEDs, such as indicating successful operation.

4. `set_io hw_clk 20`
**Purpose:** Maps `hw_clk` (hardware clock signal) to pin 20.
**Functionality:** This allows the FPGA to receive clock inputs through pin 20, which are essential for timing operations inside the FPGA.
Use Case: Used for timing-sensitive applications, such as counters, PWM signals, or high-speed data processing.

5. `set_io testwire 17`
**Purpose:** Assigns `testwire` to pin 17.
**Functionality:** This is generally used for debugging or testing, allowing a test signal to be monitored or controlled externally.
Use Case: Can be used for temporary signal monitoring, debugging FPGA behavior, or testing different pin configurations.

##### Summary
Each `set_io` command plays a crucial role in defining FPGA pin assignments. Properly mapping logical signals to physical pins ensures correct functionality of the design and prevents errors in hardware interaction. These assignments are especially important when dealing with LEDs, clocks, and debugging signals, as they directly impact how the FPGA interacts with external components.
</details>

### Step 3 Integrating with the VSDSquadron FPGA Mini Board
Integrating the VSDSquadron FPGA Mini Board involves understanding its hardware specifications, configuring the Physical Constraints File (PCF), and flashing the Verilog design onto the board. The first step is reviewing the board’s datasheet to map logical signals in the Verilog code to physical pins. Proper connections must be ensured, typically using a USB-C interface and FTDI for communication.

<details>
      <summary><STRONG>VSDSquadron FM integration</STRONG></summary>

#### Steps to Follow for Integrating the VSDSquadron FPGA Mini Board
##### 1. Review the Board Datasheet
Examine the [Datasheet](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/VSDSquadronFMDatasheet.pdf) to understand its features, pin configurations, and necessary connections.
Identify the relevant pin mappings that align with the PCF file and Verilog code.
##### 2. Establish the Physical Connections
Use the datasheet as a reference to ensure correct pin-to-signal mapping in the PCF file.
Properly connect the board to your computer using USB-C while ensuring the FTDI connection is intact.
##### 3. Build and Flash the Verilog Code
Follow the [Makefile](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%201/Makefile) instructions to compile and upload the design onto the FPGA:

**Clean Previous Builds:**
```
make clean
```
![image](https://github.com/user-attachments/assets/f04a46d0-f8ac-4110-8a3b-0ab6d49c6728)

This removes any existing compiled files to avoid conflicts.

**Compile the Design:**
```
make build
```
![image](https://github.com/user-attachments/assets/ae92a8c9-e01b-43b8-b764-aff532544da7)

This step synthesizes and prepares the Verilog design for programming.

**Flash the FPGA Board:**
```
sudo make flash
```
![image](https://github.com/user-attachments/assets/6e29b8b7-4194-460d-beba-092d80987b3f)

This uploads the bitstream to the FPGA.

##### 4. Verify the Board’s Behavior
After flashing, observe the RGB LED on the FPGA board.


[Video](https://github.com/user-attachments/assets/f6b9ed3f-f884-4059-9814-a0b347917e50)


The LED should blink, confirming a successful upload and execution of the Verilog design.

*Figure: Board after 'make clean' process*

![image](https://github.com/user-attachments/assets/edb4d36d-8c6a-4e28-bddf-f5a7a6436570)

</details>

### Step 4: Final documentation
<details>
      <summary><STRONG>Final Documentation</STRONG></summary>

#### Summary of Verilog Code Functionality
This Verilog module is designed to control an RGB LED using an internal high-frequency oscillator (SB_HFOSC) and a 28-bit frequency counter. The module routes bit 5 of the counter to a testwire for monitoring purposes. The SB_RGBA_DRV driver handles current-controlled PWM outputs, setting the blue LED at maximum brightness while keeping the red and green LEDs at minimum. This configuration ensures stable LED operation with minimal external dependencies, making it well-suited for embedded systems.

#### Pin Mapping Details (PCF File)
The Physical Constraints File  [(PCF)](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%201/VsdFpgaMini.pcf) maps FPGA logical signals to their respective hardware pins as follows:
```
Red LED → Pin 39
Blue LED → Pin 40
Green LED → Pin 41
Clock Signal → Pin 20
Testwire → Pin 17
```
   ![image](https://github.com/user-attachments/assets/47e8710a-fb94-42d5-94e4-c63b50327710)
   
This mapping aligns with the VSDSquadron FPGA Mini board datasheet, ensuring correct functionality.

#### Understanding and Implementing the Verilog Code
- Set Up the Development Environment
- Ensure that the PCF file mappings match the physical connections on the FPGA board.
- Connect the board to the computer using USB-C, ensuring proper FTDI communication.
- Compile and Flash the Verilog Code

**Run the following commands in the project directory:**
```
make clean   # Clears previous builds
make build   # Compiles the design
sudo make flash   # Programs the FPGA board
```
#### Verify LED Behavior

Once programmed, the RGB LED should start blinking.
This confirms that the FPGA has been successfully programmed.

#### Problems faced

![image](https://github.com/user-attachments/assets/109ce2af-fa2d-45d1-a89b-fa0350d4b263)
</details>
</details>
<details>
<summary><STRONG> Task 2</STRONG></summary>
  
# VSDSquadron-FM-research-internship-by-Abhimanyu-FPGA-MINI
The VSDSquadron FPGA Mini (FM) is a compact and low-cost development board designed for FPGA prototyping and embedded system projects. This board provides a seamless hardware development experience with an integrated programmer, versatile GPIO access, and onboard memory, making it ideal for students, hobbyists, and developers exploring FPGA-based designs. [(Source)](https://www.vlsisystemdesign.com/vsdsquadronfm/)
## Task 2 Implementing a UART loopback
### Step 1: Understanding the Verilog code
### UART Loopback Overview
**UART (Universal Asynchronous Receiver-Transmitter)** is a hardware communication protocol used for serial communication between devices. It operates using two primary data lines:

- **TX (Transmit) pin** – Sends data
- **RX (Receive) pin** – Receives data

A UART loopback mechanism is a test mode where transmitted data (TX) is directly fed back into the receive line (RX) of the same module. This allows verification of UART functionality without requiring an external device.

🔗 [View the existing code here](https://github.com/Arihaansingh/VSDSquadron_fpga_mini-FM-Internship_By-Arihaan_singh/blob/main/Task%202/uart_trx.v)

<details>
  <summary><STRONG> Verilog module overview</STRONG></summary>

### Analysis:

### 🚀 Module Breakdown
This module is designed to implement a UART loopback mechanism while also controlling an RGB LED based on the received data.

### ⚙️ Main Components
#### 🟢 Clock System

- Uses an Internal Oscillator (`SB_HFOSC`) to generate a clock signal.

- Configured with `CLKHF_DIV = "0b10"` to divide the frequency.

- Provides a timing reference for all operations.

#### 🔴 UART Loopback Communication

- TX (Transmit) and RX (Receive) pins are directly connected within the module.

- Any data sent to `uarttx` is immediately received at `uartrx`.

- This feature is useful for self-testing UART functionality without needing another device.

#### 🔵 Frequency Counter

- A 28-bit counter (`frequency_counter_i`) tracks oscillator cycles.

- It increases on each positive edge of the clock signal.

- Helps generate timing signals for internal operations.

#### 🟡 RGB LED Driver (SB_RGBA_DRV)

- Controls three LEDs: Red (`led_red`), Green (`led_green`), and Blue (`led_blue`).

- Uses PWM (Pulse Width Modulation) to adjust brightness levels.

- UART data is directly mapped to LED brightness, allowing for visual feedback.

### 🔁 How It Works
#### ✅ Receiving UART Data

- Data arrives at the `uartrx` pin.

- The same data is looped back to `uarttx` and sent out again.

- This confirms that UART transmission and reception are functioning correctly.

#### ✅ LED Control Based on UART Data

- The received data is used to control the intensity of the RGB LEDs.

- PWM signals regulate LED brightness based on input values.

- All three LEDs change intensity together based on UART input.

#### ✅ Clock & Timing Management

- The internal oscillator ensures stable timing.

- The frequency counter generates signals for PWM and UART operations.

  **This system efficiently tests UART communication while providing real-time LED feedback. 🌟**
  </details>
  
  ## Step 2 Design documentetion
    <details>
       <summary><STRONG> Analysis</STRONG></summary>
    <details>
     <summary><STRONG> Block diagram illustrating the UART loopback architecture</STRONG></summary>
![image](https://github.com/user-attachments/assets/36fe2f6a-95c5-4d34-b74c-1568dbffcdf5)
  </details>
  <details>
     <summary><STRONG> Detailed circuit diagram showing connections between the FPGA and any peripheral devices used</STRONG></summary> 
    
![image](https://github.com/user-attachments/assets/eaff15eb-6a90-42c3-a44e-727a38fbbf84)
  </details>
  </details>

 ## Step 3 Implementation
  <details>
       <summary><STRONG> Transmitting code to FPGA Board</STRONG></summary>
    
### 🚀 UART Loopback on VSDSquadron FPGA

**📁 Setting Up the Project**

1. Create the following files inside a new folder under VSDSquadron_FM. In this case, the folder is named uart_loopback:

📜 Files to Create:

- 🛠️ Makefile
- 💾 uart_trx- Verilog
- 🏗️ Verilog file
- 📌 pcf (Pin Constraint File)
- 📌top module

📌 Folder Structure:

```
VSDSquadron_FM/
 ├── uart_loopback/
 │   ├── Makefile
 │   ├── uart_trx.v
 │   ├── top.v
 │   ├── uart_loopback.pcf
```

### 🔌 Connecting the FPGA Board

1️⃣ Plug in the FPGA Board to your system via USB-C.

2️⃣ Verify the Connection by running:

```
lsusb
```

💡 If the board is detected, you should see:

```
Future Technology Devices International
```

### 🛠️ Building & Flashing the Code

🔹 Navigate to the Folder

```
cd VSDSquadron_FM/uart_loopback
```

🔹 Build the Design

```
make build
```

🔹 Flash the FPGA Board (Run with sudo)

```
sudo make flash
```


✔️ Congratulations! You have successfully programmed your VSDSquadron FPGA for UART loopback testing! 🚀
  </details>

 ## Step 4 Testing and Verification
  <details>
       <summary><STRONG> Testing and Verification</STRONG></summary>

### 🖥️ Setting Up Docklight for UART Loopback Testing

**📥 Download & Install Docklight**

To test the UART loopback, we will be using Docklight, a serial communication software. You can download it from the [Docklight website](https://docklight.de/downloads/)
***
**🔌 Connecting & Configuring Docklight**

1️⃣ Open Docklight

2️⃣ Verify the Communication Port

**Ensure your system (not the VM) is connected to the correct COM port.*

**Default is COM1, but in my case, it was COM7.*

**If incorrect, change it by navigating to:*

```
Tools > Project Settings
```

3️⃣ Set the Baud Rate

**Speed: 9600**
***
**✉️ Sending & Receiving Data**

**🔹 Create a Send Sequence:**

1️⃣ Double-click on the small blue box under the "Name" column in the Send Sequences panel.

2️⃣ Enter the following details:

- 🏷️ Name: (Any descriptive label for your message)
- 🔣 Format: (Choose an appropriate data format)
- ✍️ Message: (Enter the message you want to send)

3️⃣ Click "Apply" and verify that your message appears under Send Sequences.

🔹 Transmit the Message:

1️⃣ Click the ➡️ (arrow) beside the name to send the message.

2️⃣ Verify the Response in the Receive Window.

**✅ If successful, the received message should match the sent message!**
***
✔️ Congratulations! You have successfully programmed your VSDSquadron FPGA for UART loopback testing! 🚀
</details>

## Step 5 Final Documentation
    
<details>      
    <summary><STRONG>Block and Circuit diagram</STRONG></summary>
<details>
     <summary><STRONG> Block diagram illustrating the UART loopback architecture</STRONG></summary
                                                                                           
![image](https://github.com/user-attachments/assets/36fe2f6a-95c5-4d34-b74c-1568dbffcdf5)
  </details>
  <details>
     <summary><STRONG> Detailed circuit diagram showing connections between the FPGA and any peripheral devices used</STRONG></summary> 
    
![image](https://github.com/user-attachments/assets/eaff15eb-6a90-42c3-a44e-727a38fbbf84)
  </details>
  </details>
## 5.Demo video
[Demo Video] (https://github.com/user-attachments/assets/370e20cc-b9b6-417b-b359-ec561e7105f0)
</details>
</details>
<details>
<summary><STRONG> Task 3</STRONG></summary>

# VSDSquadron-FM-research-internship-by-Abhimanyu-FPGA-MINI
The VSDSquadron FPGA Mini (FM) is a compact and low-cost development board designed for FPGA prototyping and embedded system projects. This board provides a seamless hardware development experience with an integrated programmer, versatile GPIO access, and onboard memory, making it ideal for students, hobbyists, and developers exploring FPGA-based designs. [(Source)](https://www.vlsisystemdesign.com/vsdsquadronfm/)
### Task 3 UART Transmit Module

## Overview
This module implements an **8N1 UART Transmitter**, enabling serial data transmission using an **8-bit data frame, no parity bit, and 1 stop bit**. It generates a **9600 baud clock** from a 12 MHz oscillator and provides a simple state-machine-based transmission mechanism.The code for this module can accessed [here](https://github.com/Skandakm29/Vsd_squadron_mini_Fpga_3/blob/main/uart_trx.v).


## **1.Study the Existing Code**

  
## Understanding the code

### **Top Module**
The `top` module integrates the UART transmission logic with an internal **12 MHz oscillator**, a **baud rate generator**, and an **RGB LED driver**.

- **Clock Generation:** Uses an internal oscillator running at **12 MHz** to generate a **9600 Hz clock** via a **clock divider**.
- **Clock Division:** The 12 MHz clock is divided by **1250** to produce a **9600 Hz clock**. Since a clock signal toggles every half-period, it is toggled every **625 cycles** to achieve an accurate **baud rate** for UART transmission.
- **UART Transmission:** The **UART transmitter (`uart_tx_8n1`)** continuously sends the character `'D'` using an **8N1 format** (8 data bits, No parity, 1 stop bit).
- **LED Control:** The RGB LEDs toggle based on the **internal frequency counter**, creating a blinking pattern based on specific bits of the counter.

This module ensures **accurate timing for UART communication** by properly generating the required baud clock, while also controlling **visual indicators** via LED toggling.

### `uart_tx_8n1` Module
The `uart_tx_8n1` module implements a simple **UART transmitter** using a **finite state machine (FSM)** with the following states:

#### **Baud Rate Generator**
The baud rate generator derives a **9600 baud clock** from a **12 MHz input clock** using a counter-based approach.

- A **counter** increments on every rising edge of the **12 MHz clock**.
- When the counter reaches **1249**, it resets and toggles the **baud clock enable** signal.
- This effectively generates a **9600 Hz clock**.
#### **State Machine States**

- **IDLE STATE (STATE_IDLE)**
  - If senddata = 1 and the state is STATE_IDLE, it:
  - Moves to the **STATE_STARTTX** state.
  - Loads txbyte (**8-bit data to transmit**) into buf_tx.
  - Clears txdone (**indicates transmission is ongoing**).

  - Otherwise, if still in **STATE_IDLE**, it:
  - Keeps txbit **high (1)** because **UART idles at high**.
  - Ensures txdone remains **low (0)**.

- **Start Bit Transmission (STATE_STARTTX)**
  - Once in **STATE_STARTTX**, it:
  - Sets txbit **low (0)** (**start bit** in UART communication).
  - Moves to **STATE_TXING** to transmit data bits.

- **Sending Data Bits (STATE_TXING)**
  - If state == STATE_TXING and bits_sent < 8, it:
  - Sends the **Least Significant Bit (LSB) of buf_tx**.
  - Shifts buf_tx right (>> 1).
  - Increments bits_sent.

- **Stp Bit Transmission (STATE_TXDONE)**
  - After **8 data bits** are transmitted, it:
  - Sends the **stop bit (1)**.
  - Resets bits_sent to 0.
  - Moves to **STATE_TXDONE**.

- **Transmission Complete (STATE_TXDONE → STATE_IDLE)**
  - In **STATE_TXDONE**, it:
  - Sets txdone = 1 (**indicates transmission complete**).
  - Returns to **STATE_IDLE**.


## **2.System Architecture** 


## Block diagram

  ![Block diagram](https://github.com/user-attachments/assets/9c0cb07d-d1c9-4dab-aaeb-d002b9b3e716)

## Circuit diagram

  ![Circuit diagram](https://github.com/user-attachments/assets/d52e18ef-c421-4163-a42b-85cfb44e8397)



## **3.Synthesis & Programming** 

## Testing and Output

## **Clone & Setup Repository**
```bash
git clone https://github.com/Skandakm29/Vsd_squadron_mini_Fpga_3.git
cd "Vsd_squadron_mini_Fpga_3"
```

###  Build the Bitstream
```bash
make build
```
 Generates top.bin for the FPGA.

###  **Flash to FPGA**
```bash
sudo make flash
```
Uploads the bitstream to the FPGA.
### **UART Testing**
```bash
sudo make terminal
```

## **4.UART Transmission Showcase**


## Demo Video

[![Watch the Demo](https://github.com/user-attachments/assets/2e41d50e-7fb5-4c2e-9296-9f6e4c054e18)](https://github.com/user-attachments/assets/2e41d50e-7fb5-4c2e-9296-9f6e4c054e18)

</details>
</details>
  <details>
<summary><STRONG> Task 4</STRONG></summary>

# VSDSquadron-FM-research-internship-by-Abhimanyu-FPGA-MINI
The VSDSquadron FPGA Mini (FM) is a compact and low-cost development board designed for FPGA prototyping and embedded system projects. This board provides a seamless hardware development experience with an integrated programmer, versatile GPIO access, and onboard memory, making it ideal for students, hobbyists, and developers exploring FPGA-based designs. [(Source)](https://www.vlsisystemdesign.com/vsdsquadronfm/)

# Implementing a UART Transmitter that Sends Data Based on Sensor Inputs

## Objective:
Design and implement a **UART transmitter** that sends real-time **sensor data** over a serial interface. This ensures the FPGA can **communicate sensor values** reliably to an external device.

---


# 1.Study the Existing Code

## Module Analysis

### **Architecture Overview**
The `sense_uart_tx` module enables **sensor-driven UART transmission**, ensuring efficient and structured data transfer. The architecture includes:

1. **Sensor Data Handling**
2. **Baud Rate Generation**
3. **UART Transmission Logic**
4. **State Machine for Data Control**

---

### **Operation Flow**
1. **Data Sampling**
   - Sensor values are **sampled** at defined intervals.
   - A **data_valid** signal triggers the transmission process.
   - The **32-bit sensor data buffer** holds the values before transmission.

2. **Baud Rate Control**
   - The **baud rate generator** creates a stable **9600 baud clock**.
   - A counter-based approach ensures correct bit timing.

3. **UART Transmission Sequence**
   - **START**: Outputs a **low start bit**.
   - **DATA BITS**: Transmits **8-bit chunks** sequentially.
   - **STOP**: Outputs a **high stop bit** to mark the end.
   - **State transitions** synchronize the process.

4. **Status Indication**
   - The **tx_done** signal indicates completion.
   - The **ready** signal ensures no data loss during continuous sensor readings.

---

### **Port Analysis**
1. **Clock and Reset**
   - **clk**: Drives synchronous operations.
   - **reset_n**: Resets all internal states.

2. **Sensor Interface**
   - **sensor_data [31:0]**: Input from the sensor module.
   - **data_valid**: Indicates new sensor data availability.

3. **UART Output**
   - **tx_out**: UART serial output for external communication.

4. **Control Signals**
   - **tx_start**: Initiates UART transmission.
   - **tx_done**: Signals when data transmission is complete.
   - **ready**: Indicates module is ready for new data.

---

### **Internal Logic**
1. **Finite State Machine (FSM)**
   - **IDLE**: Waits for a **data_valid** trigger.
   - **START**: Sends the **start bit (0)**.
   - **DATA**: Sequentially shifts out **8-bit portions** of sensor data.
   - **STOP**: Outputs the **stop bit (1)**.
   - **DONE**: Raises `tx_done` and returns to IDLE.

2. **Baud Rate Generator**
   - Uses **clock division** to produce an accurate **9600 Hz baud clock**.

3. **Shift Register**
   - Holds **32-bit sensor data**.
   - Sequentially shifts **8 bits per transmission cycle**.


# 2.System Architecture

## Block diagram
<details>
   <summary>Block diagram</summary>

   ![Block diagram](https://github.com/user-attachments/assets/15f9116a-ffa7-4ca3-90fa-f3e1a19eab03)
   This block diagram illustrates an **FPGA-based UART transmission system** for sensor data.

### **Sensor Section**
- **Sensor Interface** → Captures raw data.
- **Data Processing** → Filters/formats the data.
- **Data Buffer** → Stores processed data before transmission.

### **FPGA Section**
- **Baud Rate Generator** → Generates clock for UART.
- **Data Buffer** → Stores sensor data for transmission.
- **TX Shift Register** → Shifts data bit by bit.
- **UART TX Logic** → Handles start, data, and stop bits.
- **State Machine** → Controls the transmission sequence.

### **Data Flow**
1. Sensor collects and processes data.
2. FPGA buffers and prepares it for UART.
3. TX Shift Register formats the data.
4. UART TX Logic transmits it serially.
5. State Machine ensures correct timing.

</details>

## Circuit Diagram

<details>
   <summary>Circuit diagram</summary>

   ![Circuit diagram](https://github.com/user-attachments/assets/de674840-445b-4f92-8f14-888fd27434d0)

</details>


#  3.Synthesis & Programming

## Testing and Output

## **Clone & Setup Repository**
```bash
git clone https://github.com/Skandakm29/vsd_squadron_minifpga_4.git
cd "Vsd_squadron_mini_Fpga_4"
```

###  Build the Bitstream
```bash
make build
```
 Generates top.bin for the FPGA.

###  **Flash to FPGA**
```bash
sudo make flash
```
Uploads the bitstream to the FPGA.
### **UART Testing**
```bash
sudo make terminal
```


# 4.UART Transmission Showcase

## Demo Video

   ![Watch the Demo](https://github.com/user-attachments/assets/84f6012c-1b44-4cf5-b4b3-b44f7cd84481)
   </details>
   </details>
   <details>
   <summary><STRONG>PROJECT - Real time Sensor Data Acquisition and Transmission System </STRONG></summary>
  
   # PROJECT - Real time Sensor Data Acquisition and Transmission System:
## Introduction
This report presents a complete design for a real-time sensor-based measurement and communication system using an FPGA. The system measures distance using the HC-SR04 ultrasonic sensor, 

processes the signal on the FPGA board, and transmits the result to a computer through UART. Included are system details, schematics, annotated Verilog modules, testing procedures, and

a video demo.

## System Details
The design consists of several interconnected components:

1. 12 MHz internal oscillator on the FPGA serves as the main clock source.

2. A timing module generates periodic measurement triggers, e.g., every 50 ms or 250 ms.

3.  ultrasonic sensor module (hc_sr04.v) emits a 10 µs trigger pulse and calculates distance based on echo return time.

4. UART transmission is handled by a module (uart_tx_8n1.v) that sends the calculated distance in ASCII format at a baud rate of 9600.

RGB LEDs can optionally display status visually. The top-level Verilog file connects all submodules. It holds the distance result, converts it to ASCII, and sends it to a PC via UART

using a USB-to-Serial adapter.

 # Block Diagram

 ![Image](https://github.com/user-attachments/assets/3c138c13-4ce2-451f-b51d-4eeb65809072)

 # Circuit Diagram

 ![Image](https://github.com/user-attachments/assets/fd626953-c339-4fa2-a0ae-04fafcc82537)

# Verilog Modules
## UART Transmitter – uart_tx_8n1.v

    // 8N1 UART Module, transmit only

    module uart_tx_8n1 (
    clk,        // input clock
    txbyte,     // outgoing byte
    senddata,   // trigger tx
    txdone,     // outgoing byte sent
    tx,         // tx wire
    );

    /* Inputs */
    input clk;
    input[7:0] txbyte;
    input senddata;

    /* Outputs */
    output txdone;
    output tx;

    /* Parameters */
    parameter STATE_IDLE=8'd0;
    parameter STATE_STARTTX=8'd1;
    parameter STATE_TXING=8'd2;
    parameter STATE_TXDONE=8'd3;

    /* State variables */
    reg[7:0] state=8'b0;
    reg[7:0] buf_tx=8'b0;
    reg[7:0] bits_sent=8'b0;
    reg txbit=1'b1;
    reg txdone=1'b0;

    /* Wiring */
    assign tx=txbit;

    /* always */
    always @ (posedge clk) begin
        // start sending?
        if (senddata == 1 && state == STATE_IDLE) begin
            state <= STATE_STARTTX;
            buf_tx <= txbyte;
            txdone <= 1'b0;
        end else if (state == STATE_IDLE) begin
            // idle at high
            txbit <= 1'b1;
            txdone <= 1'b0;
        end

        // send start bit (low)
        if (state == STATE_STARTTX) begin
            txbit <= 1'b0;
            state <= STATE_TXING;
        end
        // clock data out
        if (state == STATE_TXING && bits_sent < 8'd8) begin
            txbit <= buf_tx[0];
            buf_tx <= buf_tx>>1;
            bits_sent = bits_sent + 1;
        end else if (state == STATE_TXING) begin
            // send stop bit (high)
            txbit <= 1'b1;
            bits_sent <= 8'b0;
            state <= STATE_TXDONE;
        end

        // tx done
        if (state == STATE_TXDONE) begin
            txdone <= 1'b1;
            state <= STATE_IDLE;
        end

    end

    endmodule

## Core Features:
Implements a finite state machine (FSM) with the following states:

IDLE – Waits for transmission request.

START – Sends start bit (logic 0).

DATA – Transmits 8 bits, starting from LSB.

STOP – Sends stop bit (logic 1), then returns to IDLE.

Operates with a 9600 Hz enable signal to time each UART bit transmission step.

## Ultrasonic Sensor Module – ultra_sonic_sensor.v

    module hc_sr04 #(
    parameter ten_us = 10'd120  // ~120 cycles for ~10µs at 12MHz
    )(
    input             clk,         // ~12 MHz clock
    input             measure,     // start a measurement when in IDLE
      output reg [1:0]  state,       // optional debug: current state
      output            ready,       // high in IDLE (between measurements)
      input             echo,        // ECHO pin from HC-SR04
      output            trig,        // TRIG pin to HC-SR04
      output reg [23:0] distanceRAW, // raw cycle count while echo=1
      output reg [15:0] distance_cm  // computed distance in cm
    );

      // -----------------------------------------
      // State definitions
      // -----------------------------------------
      localparam IDLE      = 2'b00,
                 TRIGGER   = 2'b01,
                 WAIT      = 2'b11,
                 COUNTECHO = 2'b10;
    
      // 'ready' is high in IDLE
      assign ready = (state == IDLE);
    
      // 10-bit counter for ~10µs TRIGGER
      reg [9:0] counter;
      wire trigcountDONE = (counter == ten_us);

      // Initialize registers (for simulation & synthesis without reset)
      initial begin
        state       = IDLE;
        distanceRAW = 24'd0;
        distance_cm = 16'd0;
        counter     = 10'd0;
      end
    
      // -----------------------------------------
      // 1) State Machine
      // -----------------------------------------
      always @(posedge clk) begin
        case (state)
          IDLE: begin
            // Wait for measure pulse
            if (measure && ready)
              state <= TRIGGER;
          end

      TRIGGER: begin
        // ~10µs pulse, then WAIT
        if (trigcountDONE)
          state <= WAIT;
      end

      WAIT: begin
        // Wait for echo rising edge
        if (echo)
          state <= COUNTECHO;
      end

      COUNTECHO: begin
        // Once echo goes low => measurement done
        if (!echo)
          state <= IDLE;
      end

      default: state <= IDLE;
    endcase
      end
    
      // -----------------------------------------
      // 2) TRIG output is high in TRIGGER
      // -----------------------------------------
      assign trig = (state == TRIGGER);
    
      // -----------------------------------------
      // 3) Generate ~10µs trigger pulse
      // -----------------------------------------
      always @(posedge clk) begin
        if (state == IDLE) begin
          counter <= 10'd0;
        end
        else if (state == TRIGGER) begin
          counter <= counter + 1'b1;
        end 
        // No else needed; once we exit TRIGGER, we stop incrementing.
        end

      // -----------------------------------------
      // 4) distanceRAW increments while ECHO=1
      // -----------------------------------------
      always @(posedge clk) begin
        if (state == WAIT) begin
          // Reset before new measurement
          distanceRAW <= 24'd0;
        end
        else if (state == COUNTECHO) begin
          // Add 1 each clock cycle while echo=1
          distanceRAW <= distanceRAW + 1'b1;
        end
      end
    
      // -----------------------------------------
      // 5) Convert distanceRAW to centimeters
      // -----------------------------------------
      // distance_cm = (distanceRAW * 34300) / (2 * 12000000)
      always @(posedge clk) begin
        distance_cm <= (distanceRAW * 34300) / (2 * 12000000);
      end

    endmodule

    //===================================================================
    // 2) Refresher for ~50ms or ~250ms pulses
    //===================================================================
    module refresher250ms(
      input  clk,  // 12MHz
      input  en,
      output measure
    );
      // For ~50ms at 12MHz: 12,000,000 * 0.05 = 600,000
      // For ~250ms at 12MHz: 12,000,000 * 0.25 = 3,000,000
      reg [18:0] counter;
    
      // measure = 1 if counter == 1 => single‐cycle pulse
      assign measure = (counter == 22'd1);
    
      initial begin
        counter = 22'd0;
      end
    
      always @(posedge clk) begin
        if (~en || (counter == 22'd600000))  
      // change to 3_000_000 if you want 250ms
      counter <= 22'd0;
    else
      counter <= counter + 1;
      end
    endmodule
    
## FSM Operation:

IDLE → TRIGGER → WAIT → COUNTECHO → IDLE

A 10 µs trigger pulse is generated in the TRIGGER state.

Echo signal duration is counted during COUNTECHO and stored as distanceRAW.

Final distance is calculated in centimeters using:
 
    distance_cm = (distanceRAW * 34300) / (2 * 12000000);

## Measurement Interval Controller: 

Functionality:

Counts up to a predefined value (e.g., 3,000,000 cycles for 250 ms at 12 MHz).

Emits a one-clock-cycle measure pulse each time the count resets.

## Top-Level Module – top.v

Responsibilities:
Divides the 12 MHz clock to generate a 9600 Hz tick for UART transmission.

Connects the sensor, refresher, and UART modules.

Holds and converts distance to ASCII using a small FSM, then transmits via UART.

# Verification

## Testbench (ultra_sonic_sensor_tb.v):
    `include "uart_trx.v"
    `include "ultra_sonic_sensor.v"
    
    //----------------------------------------------------------------------------
    //                         Module Declaration
    //----------------------------------------------------------------------------
    module top (
      // outputs
      output wire led_red,    // Red
      output wire led_blue,   // Blue
      output wire led_green,  // Green
      output wire uarttx,     // UART Transmission pin
      input  wire uartrx,     // UART Reception pin
      input  wire hw_clk,
      input  wire echo,       // External echo signal from sensor
      output wire trig        // Trigger output for sensor
    );
    
      //----------------------------------------------------------------------------
      // 1) Internal Oscillator ~12 MHz
      //----------------------------------------------------------------------------
      wire int_osc;
      SB_HFOSC #(.CLKHF_DIV("0b10")) u_SB_HFOSC (
        .CLKHFPU(1'b1),
        .CLKHFEN(1'b1),
        .CLKHF(int_osc)
      );
    
      //----------------------------------------------------------------------------
      // 2) Generate 9600 baud clock (from ~12 MHz)
      //----------------------------------------------------------------------------
      reg  clk_9600 = 0;
      reg  [31:0] cntr_9600 = 32'b0;
      parameter period_9600 = 625; // half‐period for 12 MHz -> 9600 baud
    
      always @(posedge int_osc) begin
        cntr_9600 <= cntr_9600 + 1'b1;
        if (cntr_9600 == period_9600) begin
          clk_9600  <= ~clk_9600;
          cntr_9600 <= 32'b0;
        end
      end
    
      //----------------------------------------------------------------------------
      // 3) RGB LED driver (just tying them to uartrx for demonstration)
      //----------------------------------------------------------------------------
      SB_RGBA_DRV #(
        .RGB0_CURRENT("0b000001"),
        .RGB1_CURRENT("0b000001"),
        .RGB2_CURRENT("0b000001")
      ) RGB_DRIVER (
        .RGBLEDEN(1'b1),
        .RGB0PWM(uartrx),
        .RGB1PWM(uartrx),
        .RGB2PWM(uartrx),
        .CURREN(1'b1),
        .RGB0(led_green),
        .RGB1(led_blue),
        .RGB2(led_red)
      );
    
      //----------------------------------------------------------------------------
      // 4) Ultrasonic Sensor signals
      //    We'll assume ultra_sonic_sensor.v (hc_sr04) has output distance_cm [15:0]
      //----------------------------------------------------------------------------
      wire [23:0] distanceRAW;       // If the sensor module also provides raw
      wire [15:0] distance_cm;       // MUST exist in hc_sr04, or define it
      wire        sensor_ready;
      wire        measure;
    
      hc_sr04 u_sensor (
        .clk        (int_osc),
        .trig       (trig),
        .echo       (echo),
        .ready      (sensor_ready),
        .distanceRAW(distanceRAW),
        .distance_cm(distance_cm),  // must exist in your sensor module
        .measure    (measure)
      );
    
      //----------------------------------------------------------------------------
      // 5) Trigger the sensor every ~250 ms or 50 ms
      //----------------------------------------------------------------------------
      refresher250ms trigger_timer (
        .clk (int_osc),
        .en  (1'b1),  // always enabled
        .measure (measure)
      );
    
      //----------------------------------------------------------------------------
      // 6) Finite‐State Machine to Print distance_cm as ASCII
      //----------------------------------------------------------------------------
      reg [3:0] state;
      localparam IDLE    = 4'd0,
                 DIGIT_4 = 4'd1,
                 DIGIT_3 = 4'd2,
                 DIGIT_2 = 4'd3,
                 DIGIT_1 = 4'd4,
                 DIGIT_0 = 4'd5,
                 SEND_CR = 4'd6,
                 SEND_LF = 4'd7,
                 DONE    = 4'd8;
    
      reg [31:0] distance_reg; // latch distance_cm for division
      reg [7:0]  tx_data;
      reg        send_data;
    
      // We run this state machine at clk_9600 so we only load
      // one character per 1-bit time. (Simplistic approach.)
      always @(posedge clk_9600) begin
        // By default, don't load a new character
        send_data <= 1'b0;

    case (state)
      //-------------------------------------------------
      // IDLE: wait for sensor_ready
      //-------------------------------------------------
      IDLE: begin
        if (sensor_ready) begin
          distance_reg <= distance_cm; // store the 16-bit measurement
          state <= DIGIT_4;           // go print all digits
        end
      end

      //-------------------------------------------------
      // Print the top decimal digit (5 digits total => "00057")
      //-------------------------------------------------
      DIGIT_4: begin
        tx_data  <= ((distance_reg / 10000) % 10) + 8'h30;
        send_data <= 1'b1;
        state    <= DIGIT_3;
      end
      DIGIT_3: begin
        tx_data  <= ((distance_reg / 1000) % 10) + 8'h30;
        send_data <= 1'b1;
        state    <= DIGIT_2;
      end
      DIGIT_2: begin
        tx_data  <= ((distance_reg / 100) % 10) + 8'h30;
        send_data <= 1'b1;
        state    <= DIGIT_1;
      end
      DIGIT_1: begin
        tx_data  <= ((distance_reg / 10) % 10) + 8'h30;
        send_data <= 1'b1;
        state    <= DIGIT_0;
      end
      DIGIT_0: begin
        tx_data  <= (distance_reg % 10) + 8'h30;
        send_data <= 1'b1;
        state    <= SEND_CR;
      end

      //-------------------------------------------------
      // Carriage Return + Line Feed
      //-------------------------------------------------
      SEND_CR: begin
        tx_data   <= 8'h0D; // '\r'
        send_data <= 1'b1;
        state     <= SEND_LF;
      end
      SEND_LF: begin
        tx_data   <= 8'h0A; // '\n'
        send_data <= 1'b1;
        state     <= DONE;
      end

      //-------------------------------------------------
      // Go back to IDLE
      //-------------------------------------------------
      DONE: begin
        state <= IDLE;
      end

      default: state <= IDLE;
    endcase
      end
    
      //----------------------------------------------------------------------------
      // 7) UART Transmitter
      //----------------------------------------------------------------------------
      uart_tx_8n1 sensor_uart (
        .clk      (clk_9600),
        .txbyte   (tx_data),
        .senddata (send_data),
        .tx       (uarttx)
      );
    
    endmodule
    
Simulates measure and dummy echo signals.

Outputs waveforms to wave.vcd.

Validates that the COUNTECHO timing matches the computed distance.


# On-Board Testing
## Connections:
TRIG pin → Sensor TRIG, ECHO pin ← Sensor ECHO

3 V power to sensor, shared GND with FPGA

UART TX from FPGA to PC via USB–Serial adapter

## PC Terminal Setup:

Baud rate: 9600


Data bits: 8


Parity: None


Stop bits: 1


## Testing Method:
Position an object approximately 10 cm from the sensor.

The terminal should display a reading near "00010".

Moving the object should change the displayed distance dynamically.


# Video Output

# Compilation and Running

    make build

This runs the required synthesis tools (Yosys, nextpnr, icepack) to generate the bitstream file (e.g., top.bin).

    sudo make flash

Uploads the bitstream to the FPGA board using iceprog or similar.

    sudo make terminal

Launches a terminal window (e.g., using screen or minicom) at 9600 baud to monitor the distance readings. (Serial Terminal)

# Video Output

https://github.com/user-attachments/assets/5a92950a-49de-4bc7-b689-9e6aeaa5128a
# Conclusion
This project successfully implements a real-time sensor interface using FPGA:\

Periodic distance measurement with HC-SR04


Real-time echo counting and distance calculation


ASCII-based serial communication at 9600 baud


Optional local display using RGB LEDs


The design provides a foundation that can be expanded with additional sensor types, error correction, or enhanced UART configurations.

# Acknowledgements 
Kunal Ghosh, Co-founder, VSD Corp. Pvt. Ltd. Nickson P Jose, Physical Design Engineer, Intel Corporation.
