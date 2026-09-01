# Bluetooth Linux Device Driver

A simple Linux kernel module demonstrating communication with a serial device using the **Linux SerDev framework**.

This project is intended as a learning example for understanding how a Linux kernel driver communicates with a Bluetooth controller or other UART-based devices.

## Overview

The driver performs the following operations:

* Registers a SerDev device driver with the Linux kernel.
* Matches the device using the Device Tree compatible string.
* Opens the UART/SerDev device.
* Configures the UART baud rate to **115200**.
* Disables hardware flow control.
* Configures **no parity**.
* Sends the message:

```text
Hello
```

* Receives data from the serial device.
* Prints received data in hexadecimal format using kernel logs.
* Properly closes the SerDev device when the driver is removed.

## Project Structure

```text
Bluetooth-linux-device-driver-
│
├── hello_bt_servdev.c.txt
├── hello_bt_servdev.dts.txt
└── Makefile.mak
```

### File Description

#### `hello_bt_servdev.c.txt`

This is the main Linux kernel driver source code.

It includes:

* SerDev driver registration
* UART configuration
* Transmit functionality
* Receive callback
* Probe and remove functions
* Device Tree matching
* Kernel module initialization and cleanup

#### `hello_bt_servdev.dts.txt`

This file contains the build configuration for the kernel module.

It defines:

* Module name
* Kernel build directory
* Build command
* Clean command
* Module insertion command
* Module removal command

## Driver Flow

```text
                Linux Kernel
                     │
                     ▼
           Register SerDev Driver
                     │
                     ▼
              Device Match
          (Device Tree Compatible)
                     │
                     ▼
                Probe()
                     │
                     ▼
            Open SerDev Device
                     │
                     ▼
          Configure UART Settings
             Baud: 115200
                     │
                     ▼
              Send "Hello"
                     │
                     ▼
          Receive Data Callback
                     │
                     ▼
           Print Data to dmesg
```

## Important Functions

### `hello_bt_probe()`

This function is called when the driver successfully matches with the SerDev device.

The driver:

1. Registers the SerDev client operations.
2. Opens the SerDev device.
3. Sets the baud rate to `115200`.
4. Disables flow control.
5. Sets parity to `NONE`.
6. Sends the message `"Hello\r\n"`.

### `hello_bt_receive()`

This callback is called when data is received from the UART device.

The received data is printed using:

```c
print_hex_dump();
```

This helps in debugging the communication between the Linux driver and the connected device.

### `hello_bt_write_wakeup()`

This callback notifies the driver when the SerDev subsystem is ready to accept more transmitted data.

### `hello_bt_remove()`

This function is called when the driver is removed.

It closes the SerDev device and performs cleanup.

## Building the Module

### Prerequisites

Make sure the following are installed:

* Linux operating system
* Linux kernel headers
* GCC compiler
* Make

Install kernel headers on Ubuntu/Debian:

```bash
sudo apt update
sudo apt install build-essential linux-headers-$(uname -r)
```

### Rename the Files

For easier compilation, rename the files:

```bash
mv hello_bt_servdev.c.txt hello_bt_serdev.c
mv Makefile.mak Makefile
```

Make sure the source filename matches the module object name in the Makefile.

### Build

Run:

```bash
make
```

This should generate the kernel module:

```text
hello_bt_serdev.ko
```

## Loading the Module

Insert the module:

```bash
sudo insmod hello_bt_serdev.ko
```

Check kernel logs:

```bash
dmesg | tail -n 20
```

You may see messages similar to:

```text
hello_bt_serdev: module loaded
hello_bt_serdev: probe started
hello_bt_serdev: UART opened at 115200 baud
hello_bt_serdev: sent bytes: Hello
```

## Removing the Module

Remove the module:

```bash
sudo rmmod hello_bt_serdev
```

Check the logs:

```bash
dmesg | tail -n 20
```

## Device Tree Matching

The driver uses the following compatible string:

```c
"example,hello-bt-serdev"
```

The corresponding UART/SerDev device in the Device Tree must use the same compatible string for the driver to bind correctly.

Example concept:

```dts
bluetooth {
    compatible = "example,hello-bt-serdev";
};
```

> The actual Device Tree configuration depends on the target board and UART controller.

## Learning Objectives

This project helps in understanding:

* Linux kernel modules
* Linux device drivers
* SerDev framework
* UART communication in the Linux kernel
* Device Tree matching
* Probe and remove functions
* Kernel logging using `pr_info()` and `pr_err()`
* Receiving serial data through callbacks
* Sending data using `serdev_device_write_buf()`

## Future Improvements

This project can be extended to support:

* Real Bluetooth HCI communication
* HCI command transmission
* HCI event reception
* UART flow control
* Bluetooth controller initialization
* Firmware download
* BlueZ integration
* Power management
* GPIO reset control
* Device Tree configuration for a real Bluetooth chip

## Author

**Harsha**

## Repository

[Bluetooth Linux Device Driver Repository](https://github.com/harshanishani/Bluetooth-linux-device-driver-/tree/main)

## License

This project is licensed under the **GPL license**, as specified in the kernel module source code.
