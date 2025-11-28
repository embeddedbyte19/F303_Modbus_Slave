# F303_Modbus_Slave
STM32->RS485->Screen

# STM32 Modbus RTU Slave (Function Code 0x04)

## Project Overview
This project implements a **Modbus RTU Slave** on an STM32 microcontroller using **HAL drivers and interrupt-based UART communication**. The slave responds to **Function Code 0x04 (Read Input Registers)** and returns data from a predefined register array.
The implementation is designed to be cleanly integrated into **STM32CubeMX / STM32CubeIDE generated code** without modifying auto-generated sections, ensuring maintainability and regeneration safety.

## Detailed Overview
This project demonstrates a complete Modbus RTU slave implementation on an STM32 microcontroller, focused on reliability and clarity for both learning and practical deployment. The slave continuously listens for Modbus RTU requests over UART, validates each frame using CRC16, and responds only when the request matches its configured Slave ID.

When a valid Read Input Registers (Function Code 0x04) request is received, the firmware extracts the starting address and number of registers, reads corresponding values from a predefined register array, and sends a properly formatted Modbus RTU response frame back to the master via RS485.

The architecture separates Modbus protocol logic from CubeMX-generated code, making it safe to regenerate hardware configuration without breaking Modbus functionality. This makes the project ideal for scalable embedded systems requiring industrial communication.

## Features
* Modbus RTU Slave implementation
* Supports Function Code: **0x04 - Read Input Registers**
* RS485 direction control using GPIO
* CRC16 validation as per Modbus RTU standard
* Interrupt-based UART reception
* Frame detection using silent interval timing
* Exception handling for:

  * Illegal Function
  * Illegal Data Address
  * Illegal Data Value

##  File Structure
├── Core/
│   ├── Src/
│   │   ├── main.c
│   │   └── modbus_slave.c
│   └── Inc/
│       └── modbus_slave.h

### modbus_slave.h
Defines Modbus configuration, constants, data structures, and function prototypes.

### modbus_slave.c
Contains full Modbus logic including:
* CRC calculation
* Frame processing
* Exception handling
* Response generation

### main.c
Initializes peripherals and calls Modbus functions in the main loop.

##  Hardware Requirements
* STM32F3 series MCU
* RS485 Transceiver (e.g., MAX485)
* USB-to-RS485 converter for PC
* Modbus Master tool (e.g. ModScan, QModMaster)

##  Configuration Parameters
Located in `modbus_slave.h`:

#define SLAVE_ADD 1
#define BASE_ADDR 0
#define ARRAY_SIZE 179
#define MAX_MODBUS_FRAME_SIZE 256
#define MODBUS_TIMEOUT_MS 100

##  How It Works
1. UART receives Modbus data byte-by-byte using interrupt.
2. Frame completion detected by timing gap (>10ms).
3. Frame validated using CRC.
4. Function code checked.
5. If valid, response is generated with requested register values.
6. RS485 DE pin enabled during transmission.

### Example Request
| Parameter  | Value |
| ---------- | ----- |
| Slave ID   | 1     |
| Function   | 04    |
| Start Addr | 0     |


##  How To Run
1. Open project in STM32CubeIDE
2. Build and flash to STM32 board
3. Connect RS485 module
4. Open ModScan or Master
5. Configure:

   * Baud Rate: 115200
   * Parity: None
   * Stop Bits: 1
   * Slave ID: 1
6. Send Read Input Registers request

##  Supported Modbus Function

| Function Code | Description          |
| ------------- | -------------------- |
| 0x04          | Read Input Registers |


## Notes
* Ensure correct UART and GPIO configuration.
* Verify RS485 direction pin wiring.
* CRC mismatches are silently ignored (no response).
* Frame timeout is based on HAL_GetTick().


## Typical Response Format
[Slave ID][Function][Byte Count][Data...][CRC_L][CRC_H]

## Future Enhancements

* Add support for Function Code 0x03
* Dynamic register updates
* Modbus RTU master mode
* Error logging via UART console
