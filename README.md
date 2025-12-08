# **DVB TDT/TOT Injector**

A simple, lightweight command-line utility for Windows and Linux that generates and broadcasts DVB Time and Date Table (TDT) and Time Offset Table (TOT) packets over a local UDP port.

This tool is a compiled version of the excellent dvb_tdt project by neo7530, adapted for use as a companion utility for the HackDVB GUI project.

## **What It Does**

The tdt executable's sole purpose is to create a continuous stream of MPEG Transport Stream (TS) packets containing TDT and TOT tables.

TDT (Time and Date Table): Contains the current Coordinated Universal Time (UTC).

TOT (Time Offset Table): Contains the TDT information plus the local time offset, allowing receivers to calculate the local time.

These packets are broadcast to a specified UDP port on 127.0.0.1 (localhost).

## **Why Is This Needed?**
A DVB receiver (like a satellite set-top box) has no way of knowing the current time on its own. It relies entirely on the TDT/TOT packets within the broadcast stream to set its internal clock.

Without a correct and synchronized clock, the receiver cannot properly interpret the Electronic Program Guide (EPG) data. It might show the wrong 'now/next' information, fail to display the schedule at all, or be unable to schedule recordings.

This utility ensures the receiver's clock is always synchronized, making the EPG reliable and functional.

* **Usage**
The program is controlled via a single command-line argument.

* **Syntax**
plaintext
tdt.exe <port>
<port>: The UDP port on which to broadcast the TDT/TOT packets.
Example
plaintext
tdt.exe 32000
This will start broadcasting TDT/TOT packets to udp://127.0.0.1:32000. The process will run until it is manually terminated (e.g., by pressing Ctrl+C or by the calling application).

* **Integration with TSDuck**
This tool is designed to be used with a transport stream processor like TSDuck. The main broadcast application (e.g., HackDVB GUI) pipes its primary stream into TSDuck, which then uses the datainject plugin to merge the time packets from this tool into the final multiplex.

### **A typical TSDuck command would look like this:**

* **The 'tsp' command receives a stream from another source (like ffmpeg)**
**and injects the TDT/TOT packets from this tool before outputting to hardware.**

* **tsp -I file - \ -P datainject --udp --local-port 32000 -pid 0x14 \ -O dektec ...**

* **--local-port 32000: Tells TSDuck to listen on the same port tdt.exe is broadcasting to.
* --pid 0x14: Injects the packets into PID 20 (0x14), the standard PID for TDT/TOT.**

### **Building from Source**
This is a simple C program with no external dependencies. You can compile it on Windows or Linux using GCC or a similar C compiler.

### **On Linux**

* **gcc -o tdt tdt.c**

### **On Windows (using MinGW)**

* **gcc -o tdt.exe tdt.c**


### **Full credit for the original source code and logic goes to [neo7530](https://github.com/neo7530/) and the [dvb_tdt project](https://github.com/neo7530/dvb_tdt). This repository simply provides a pre-compiled binary and documentation for its integration with the [HackDVB GUI](https://github.com/GamerA1-99/HackDVB-GUI) .**
