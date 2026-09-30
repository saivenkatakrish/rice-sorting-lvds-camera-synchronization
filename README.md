# 5m LVDS Camera Synchronization & 63-Channel Ejector Control System

## Overview

This project presents a system-level hardware design for a dual-camera grain sorting machine.

The system uses two high-speed line-scan camera processing boards:

- Front Camera PCB — Master
- Rear Camera PCB — Slave

The two boards are connected through a 5 metre Cat5e STP cable carrying LVDS differential signals.

The design focuses on real-time camera synchronization, reliable data transfer, simultaneous ejector control, and fail-safe operation.

## System Requirements

The design is based around the following requirements:

- Two line-scan cameras positioned approximately 5 metres apart
- 40 MHz FPGA processing clock
- 4096 pixels per line
- 56 µs line-scan period
- Real-time synchronization between front and rear cameras
- Simultaneous control of 63 ejector channels
- Reliable communication over a 5 metre cable
- Fail-safe behavior during communication failure

## System Architecture

The overall system is divided into two PCBs.

### Front Camera PCB — Master

The Front PCB contains:

- Lattice ECP5 LFE5U-25F-6BG381I FPGA
- Camera Link interface
- SN65LVDS047D LVDS driver
- W25Q32JVSS SPI Flash
- 3.3 V power architecture
- JTAG debug interface

The FPGA handles camera data processing, defect detection, packet generation, and LVDS clock/data transmission.

### Rear Camera PCB — Slave

The Rear PCB contains:

- Lattice ECP5 LFE5U-25F-6BG381I FPGA
- Camera Link interface
- SN65LVDS048A LVDS receiver
- 100 Ω differential termination
- TPL7407L driver arrays
- 63 ejector outputs
- 3.3 V power architecture
- 24 V solenoid power section
- JTAG debug interface

The Rear FPGA receives the serialized decision data, validates the packet, combines front and rear detection results, and updates all ejector outputs simultaneously.

## LVDS Communication Link

The two PCBs communicate through a 5 metre shielded Cat5e STP cable.

Three twisted pairs are used for:

- CLK+/CLK-
- DATA+/DATA-
- SYNC+/SYNC-

The fourth twisted pair is used for ground return.

The design uses 100 Ω termination at the receiver side to match the cable impedance and reduce signal reflections.

## Communication Protocol

The system uses an 88-bit packet:

```text
[ Start of Frame ][ Command ][ 64-bit Payload ][ CRC-8 ]
      8 bits         8 bits        64 bits        8 bits
