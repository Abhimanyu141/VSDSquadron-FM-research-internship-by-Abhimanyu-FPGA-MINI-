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
