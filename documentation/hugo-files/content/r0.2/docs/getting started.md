---
title: Getting Started
type: docs
rev: r0.2
---

# Getting Started

First time using an FPGA? This guide should cover everything you need to get the example verilog project built and loaded up on the OrangeCrab.

## Step 1: Toolchain Setup
---

This guide will make use of the Open Source FPGA toolchain. It is possible to build and install these from scratch, but the whole process takes around 30 minutes. So it's generally easier to simply use a nightly build from [Open Tool Forge](https://github.com/open-tool-forge/)

---

Grab the [latest](https://github.com/open-tool-forge/fpga-toolchain/releases) build for your platform, extract them somewhere on your PC, and add the `fpga-toolchain\bin` folder to your path:

 - MacOS: `export PATH=[path-to-bin]:$PATH`
 - Linux: `export PATH=[path-to-bin]:$PATH`
 - Windows `Powershell: $ENV:PATH = "[path-to-bin];" + $ENV:PATH`
 - Windows `cmd.exe: PATH=[path-to-bin];%PATH%`

Check that they've been correctly installed by running `yosys -V` or `nextpnr-ecp5 -V`

```bash
$ yosys -V
Yosys 0.9+2406 (open-tool-forge build) (git sha1 c0bcbe1f, gcc 9.3.0-10ubuntu2 -Os)

$ nextpnr-ecp5 -V
nextpnr-ecp5 -- Next Generation Place and Route (Version nightly-20200707)
```

> Note: The above steps only set the PATH variable for the current terminal session. Depending on your platform there are options to ensure the tools are kept in your path. 

## Step 2: Verilog Example
---
Download the [example](https://github.com/gregdavill/OrangeCrab-examples) repository. We'll build the verilog/blink example to test that everything is working.
```bash
$ git clone https://github.com/gregdavill/OrangeCrab-examples
$ cd OrangeCrab-examples/verilog/blink
$ make
    ... snip ...
Info: [ 81336,  81410) |* 
Info: [ 81410,  81484) | 
Info: [ 81484,  81558) | 
Info: [ 81558,  81632) |* 
ecppack --compress --freq 38.8 --input blink_out.config --bit blink.bit
cp blink.bit blink.dfu
dfu-suffix -v 1209 -p 5af0 -a blink.dfu
dfu-suffix (dfu-util) 0.9

Copyright 2011-2012 Stefan Schmidt, 2013-2014 Tormod Volden
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Suffix successfully added to file
```

Next, while holding the button on the OrangeCrab, plug it in. This enters the bootloader and enables you to upload new gateware. 
The LED sholud be smoothly fading through different colours.

```bash
$ make dfu
dfu-util -D blink.dfu
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

Match vendor ID from file: 1209
Match product ID from file: 5af0
Opening DFU capable USB device...
ID 1209:5af0
Run-time device DFU version 0101
Claiming USB DFU Interface...
Setting Alternate Setting #0 ...
Determining device status: state = dfuIDLE, status = 0
dfuIDLE, continuing
DFU mode device DFU version 0101
Device returned transfer size 4096
Copying data from PC to DFU device
Download	[=========================] 100%        99314 bytes
Download done.
state(7) = dfuMANIFEST, status(0) = No error condition is present
state(8) = dfuMANIFEST-WAIT-RESET, status(0) = No error condition is present
Done!
```

You should now see the LED blinking through colours!

> On linux if dfu-util throws an error you may need to add the following udev rule:
>
> `ATTRS{idVendor}=="1209", ATTRS{idProduct}=="5af0", MODE="0666", GROUP="plugdev", TAG+="uaccess"`