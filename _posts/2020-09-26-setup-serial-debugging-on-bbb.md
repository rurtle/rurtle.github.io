---
layout: 	post
title: 		Serial debug setup on BeagleBone Black
tags:		embedded beaglebone linux serial-debug tech
permalink: 	/blog/:year/:title
---

Last week I got a shiny new [BeagleBone Black (BBB)][BeagleBoneBlack]{:target="_blank"} delivered at my doorstep. Now that I had it lying on my study table, needed to figure out a way to talk to it over the serial port. In this post, I am going to show three different software based ways to communicate with your BBB (or other embedded devices, so long as those support [serial communication][serial_communication]{:target="_blank"}) from your PC running some variant of Linux. Things you will need -

* A BeagleBone Black. :innocent:
* PL2303, CP2102 or FTDI module to connect your BBB to your PC's USB port. I am using a CP2102 module purchased from Amazon for this exercise.
* Download cables (usually comes with the serial-usb adapter) for Xfering bits from the board to your PC.
* Laptop/desktop running on some Linux distro. I have Ubuntu 18.04 LTS on my setup.
* A reliable Internet connection (needed for installing the s/w tools).
* [Optional] An USB hub, if you are planning on working with multiple serial-usb adapters.

Now, let's get the hardware configuration out of the way first before we delve into software realm. Below table explains interconnection between BBB and the CP2102 serial-usb adapter. <br />

| Sr No | BBB Pin Configuration | CP2102 Module |
| :-: | :-: | :-: |
1 | GND (Pin **1**) | GND (module specific)
2 | **R**X (Pin **4**) | **T**XD (module specific)
3 | **T**X (Pin **5**) | **R**XD (module specific)

Just remember to connect the TX of BBB to RX of the serial-usb adapter and vice versa. That's all!

![PinConfig-USBSerial](/assets/BBB-PinConfig-USBSerial.jpeg)

With the BBB powered down, connect the serial-usb adapter to your PC (or USB hub connected to your PC) and power it up. Time to talk to the BBB via serial-usb adapter!

# Method 1: Using [Minicom][Minicom]{:target="_blank"} (most common)

Install minicom by running command `sudo apt install minicom`. Platform specific instructions on installation may vary though; I shall leave that for you guys to figure out. :smiley:<br />
Upon installation, launch `minicom` from the terminal. You may have to do so as <ins>`root`</ins>. Now, it's time to change the default configuration to shown as below -

{:refdef: style="text-align: center;"}
![minicom-config](/assets/minicom-serial_port_config.png)
{: refdef}

In particular, I had to explicitly turn off <ins>`Hardware Flow Control`</ins> because keyboard inputs were not being recognized. `Software Flow Control` was turned off by default; so, guess that probably was not the issue.

![bbb-minicom-console](/assets/bbb-minicom-console.png)

With the aforementioned set of changes, I was able to get BBB configured to communicate over serial port. Voila!

# Method 2: Using [Picocom][Picocom]{:target="_blank"}

Since I was not able to get keyboard inputs recognized on Minicom initially, I started looking for suitable alternatives. That is how I stumbled on Picocom. Instructions for installing Picocom is pretty similar to that of Minicom, just use `apt` (or your platform specific package management utility) to get it installed. Once the installation is complete, run the command -

    sudo picocom -b 115200 -r -l /dev/ttyUSB0

Notice that we are passing the configuration options as command line parameters here. And this is how the Picocom console should look like -

![picocom-console](/assets/picocom-console.png)

Unlike minicom though, I didn't have to specifically tweak configurations to get it to work which is nice! Further details on `picocom` can be found in the `man pages`. :wink:

# Method 3: Using [Gtkterm][Gtkterm]{:target="_blank"}

To install `gtkterm`, simply run the command `sudo apt install gtkterm` on your terminal.

{:refdef: style="text-align: center;"}
![Gtkterm-config](/assets/Gtkterm-Serial_port_config.png)
{: refdef}

For installing it on other platforms, please refer to platform specific documentation. Once installed, you may need to launch it as `root` and configure it to select port as `/dev/ttyUSB0`, baud rate as `115200` and flow control as `none`. And there you have it!

![Gtkterm-GUI](/assets/Gtkterm-GUI.png)

And here is the Gtkterm console as shown above. You can interact with your embedded board through console now that you have necessary configurations taken care of. Kudos!

And that's it! You have some high level pointers on how to debug your embedded device over serial port. For now I am sticking with `minicom` since I am more familiar with its configuration options. But it may change in the near future depending on the platform needs and other restrictions. If you have any questions/tips/suggestions, please feel free to drop me a line.

# Credits

[1] [Beagleboard: BeagleBone Black Serial][BBB-Serial]{:target="_blank"} <br />
[2] [How to Connect the BeagleBone Black via Serial over USB][BBB-Serial-config]{:target="_blank"} <br />
[3] [AskUbuntu: Minicom doesn't work][askubuntu-minicom-doesnt-work]{:target="_blank"} <br />



[BeagleBoneBlack]: https://beagleboard.org/black
[serial_communication]: https://en.wikipedia.org/wiki/Serial_communication
[Minicom]: https://en.wikipedia.org/wiki/Minicom
[Picocom]: https://github.com/npat-efault/picocom
[Gtkterm]: https://www.linuxlinks.com/gtkterm/
[BBB-Serial]: https://elinux.org/Beagleboard:BeagleBone_Black_Serial
[BBB-Serial-config]: https://www.dummies.com/computers/beaglebone/how-to-connect-the-beaglebone-black-via-serial-over-usb/
[askubuntu-minicom-doesnt-work]: https://askubuntu.com/questions/638378/minicom-doesnt-work
