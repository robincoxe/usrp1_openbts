# Altera Quartus II Project to recreate USRP1 Cyclone FPGA bitstream for OpenBTS 

Brought to you by Robin Coxe of [*Close-Haul Communications, Inc.*](http://www.close-haul.com/).
Created with Altera Quartus II Version 10.1 Build 153 for use on a USRP1 Rev. 4 board with a 52 MHz clock.  The bitstream may work with a 64 MHz clock, but it has not been tested.

DISCLAIMER: THIS PROGRAM IS DISTRIBUTED IN THE HOPE THAT IT WILL BE USEFUL, BUT WITHOUT ANY WARRANTY. IT IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND, EITHER EXPRESSED OR IMPLIED, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE. THE ENTIRE RISK AS TO THE QUALITY AND PERFORMANCE OF THE PROGRAM IS WITH YOU. SHOULD THE PROGRAM PROVE DEFECTIVE, YOU ASSUME THE COST OF ALL NECESSARY SERVICING, REPAIR OR CORRECTION.

Again, USE AT YOUR OWN RISK! If you have OpenBTS-specific questions about the design, please ask them on the OpenBTS mailing list (openbts-discuss@lists.sourceforge.net). If you aren't familiar with the Altera FPGA design flow, please consult the Altera website or Google for tutorials.  Unfortunately, I don't have time to help with basic FPGA design questions.

You can download the Quartus II Web Edition tools from [*Altera*](https://www.altera.com/download/dnl-index.jsp).  

The Altera project file is ./usrp1_openbts/toplevel/usrp_openbts/usrp_inband_usb.qpf
The changes from the usrp_inband_usb version of the USRP1 FPGA code provided by Ettus are minor.  The timestamp clock in tx_buffer_inband.v and rx_buffer_inband.v must be incremented at the GSM symbol rate, not the FPGA clock rate (52 MHz as coded), in order to be compatible with the timestamping scheme in OpenBTS.

The project revision is called usrp_openbts and generates a bitstream called usrp_openbts.rbf.

To use this bitstream with OpenBTS, you must make a few minor modifications:
1) Copy the FPGA bitstream usrp_openbts.rbf to /usr/local/share/usrp/rev4: $ sudo cp usrp_openbts.rbf /usr/local/share/usrp/rev4  (...or wherever gnuardio installed the usrp bitstreams on your machine.  You should see a bitstream called std_inband.rbf in the directory, which is the default OpenBTS Cyclone FPGA bitstream for which the original design has been lost forever).

2) In the OpenBTS source code, navigate to the Transceiver52M directory and modify USRPDevice.cpp to load the correct FPGA bitstream:  in the subroutine USRPDevice::make(bool wSkipRx), change the line:
string_rbf = "std_inband.rbf"; to string_rbf="usrp_openbts.rbf";

3) In USRPDevice.h in the same directory, change the PINGOFFSET static constant from 272 to 278 to ensure the correct timing advance calculation: 
static const TIMESTAMP PINGOFFSET = 272; to
static const TIMESTAMP PINGOFFSET = 278;


4) Remake the transceiver executable in TRANSCEIVER52M: 
$ make clean 
$ make










