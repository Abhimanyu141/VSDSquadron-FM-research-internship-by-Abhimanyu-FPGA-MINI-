## 🛠️ Project Overview

### Objective
Develop a mini digital oscilloscope that can:
- Sample analog signals using the MCP3008 ADC.
- Process the data on the VSD Squadron FPGA Mini.
- Display waveforms on a 1.3" SPI OLED screen.
- Allow user interaction through push buttons for adjusting settings.

---

## 🔧 Hardware Setup

1. **Signal Conditioning**
   - Use the LM358 op-amp to scale and offset the input analog signal to match the 0-3.3V range suitable for the MCP3008 ADC.

2. **Connecting MCP3008 to FPGA**
   - **MCP3008 Pins:**
     - VDD → 3.3V
     - VREF → 3.3V
     - AGND & DGND → Ground
     - CLK, DOUT (MISO), DIN (MOSI), CS → Connect to FPGA GPIOs

3. **Connecting OLED Display to FPGA**
   - **OLED Pins:**
     - GND → Ground
     - VCC → 3.3V
     - SCL → FPGA GPIO (SPI Clock)
     - SDA → FPGA GPIO (SPI Data)
     - RES, DC, CS → FPGA GPIOs

4. **Push Buttons**
   - Connect one terminal to FPGA GPIO and the other to ground. Use internal pull-up resistors in the FPGA or external pull-up resistors.

---

## 🧑‍💻 Verilog Modules

1. **SPI Master for MCP3008 (`spi_master.v`)**
   - Handles SPI communication to read analog values from the MCP3008.

2. **ADC Controller (`adc_controller.v`)**
   - Initiates SPI transactions and processes the received data.

3. **OLED Driver (`oled_driver.v`)**
   - Manages communication with the OLED display to render waveforms.

4. **Waveform Buffer (`waveform_buffer.v`)**
   - Stores sampled data for display.

5. **Button Debouncer (`debouncer.v`)**
   - Debounces push button inputs.

6. **Top Module (`top.v`)**
   - Integrates all modules and defines the overall system behavior.

---

## 🧪 Simulation and Testing

1. **Simulate Individual Modules**
   - Use simulation tools like Icarus Verilog and GTKWave to test each module.

2. **Integrate and Simulate the System**
   - Combine modules and simulate the entire system to ensure correct interaction.

3. **Hardware Testing**
   - Program the FPGA with the synthesized bitstream.
   - Connect the hardware components as described.
   - Use a function generator or a variable voltage source to test the oscilloscope functionality.

---








</details>
