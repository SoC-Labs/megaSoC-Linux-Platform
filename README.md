# megaSoC Linux Platform
This project contains necessary files to build a linux platform for SoC Labs megaSoC

# Boot process
ROM
 ↓
U-Boot (runs in EL2)
 ↓
Linux (runs in EL1)
 ↓
Userspace (EL0)


| Name          | Location                    |
| ------------- | --------------------------- |
| BootROM       | Silicon                     |
| SPL           | QSPI                        |
| U-Boot proper | QSPI or RAM (loaded by SPL) |
| Linux kernel  | SD card                     |


Stage 0 — ROM (hardware)

Responsibilities:
- Initialize minimal CPU state
- Initialize stack (optional)
- Load next stage from flash/SD/DDR
- Jump to it in EL2 or EL3 depending on design

Stage 1 — U-Boot (EL2)
- U-Boot responsibilities in your system:
- Hardware bring-up:
    - Confirm UART works
    - Confirm DDR is stable
    - Set up basic clocks (if not already done by ROM)
- CPU state setup:
    - Ensure running in EL2
    - Disable/ignore EL3 completely (if present)
- Memory setup:
    - Enable MMU (optional but common in U-Boot proper)
    - Map:
        - DDR
        - UART
        - GIC
        - device tree
- Device tree:
    - Load DTB from flash or embedded
    - Fix up memory size / chosen node
- Kernel loading:
    - Load Linux Image
    - Load DTB
    - Jump to kernel

Stage 2 — Linux kernel (EL1)

Linux assumes:
- EL2 already configured (or at least available)
- Memory map is sane
- Interrupt controller is initialized in DTB
- Timer is working

Linux then takes over completely.
# Status
Currently megaSoC is under development but getting a feel for how we boot the device and add linux. 
This may end up as a stale repo for a little while until we get the FPGA implementation of megaSoC fully working with DDR.