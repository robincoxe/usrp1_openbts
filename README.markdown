# Altera Quartus II Project to recreate USRP1 Cyclone FPGA bitstream for OpenBTS

Created with Altera (now Intel FPGA) Quartus II Version 10.1 Build 153 for use on a USRP1 Rev. 4 board with a 52 MHz clock.  The bitstream may work with a 64 MHz clock, but it has not been tested.

DISCLAIMER: THIS PROGRAM IS DISTRIBUTED IN THE HOPE THAT IT WILL BE USEFUL, BUT WITHOUT ANY WARRANTY. IT IS PROVIDED "AS IS" WITHOUT WARRANTY OF ANY KIND.

Again, USE AT YOUR OWN RISK! If you have OpenBTS-specific questions about the design, please ask them on the OpenBTS mailing list (openbts-discuss@lists.sourceforge.net). If you aren't familiar with the Altera FPGA design flow, please consult the Altera (now Intel FPGA) website or Google for tutorials.

You can download the Quartus II Web Edition tools from [*Altera*](https://www.altera.com/download/dnl-index.jsp).

UPDATE (MARCH 2012):  You need Version 11.0 or older of Quartus II to handle the Cyclone family with the free Web Edition tools!
If it's no longer available from the link above, try: ftp://ftp.altera.com/outgoing/release/ 

The Altera project file is ./usrp1_openbts/toplevel/usrp_openbts/usrp_inband_usb.qpf.

The changes from the usrp_inband_usb version of the USRP1 FPGA code provided by Ettus are minor.  The timestamp clock in tx_buffer_inband.v and rx_buffer_inband.v must be incremented at the GSM symbol rate, not the FPGA clock rate (52 MHz as coded), in order to be compatible with the timestamping scheme in OpenBTS.

The project revision is called usrp_openbts and generates a bitstream called usrp_openbts.rbf.

To use this bitstream with OpenBTS, you must make a few minor modifications:

1) Copy the FPGA bitstream usrp_openbts.rbf to /usr/local/share/usrp/rev4:

$ sudo cp usrp_openbts.rbf /usr/local/share/usrp/rev4

(...or wherever gnuradio installed the usrp bitstreams on your machine.  You should see a bitstream called std_inband.rbf in the directory, which is the default OpenBTS Cyclone FPGA bitstream for which the original design has been lost forever).

2) In the OpenBTS source code, navigate to the Transceiver52M directory and modify USRPDevice.cpp to load the correct FPGA bitstream:  in the subroutine USRPDevice::make(bool wSkipRx), change the line:

string_rbf = "std_inband.rbf";

to

string_rbf="usrp_openbts.rbf";

3) In USRPDevice.h in the same directory, change the PINGOFFSET static constant from 272 to 278 to ensure the correct timing advance calculation:

static const TIMESTAMP PINGOFFSET = 272;

to

static const TIMESTAMP PINGOFFSET = 278;


4) Remake the transceiver executable in TRANSCEIVER52M:

$ make clean

$ make

