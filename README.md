# About #

Put your servers in a different room, and control them from a raspberry pi via an RS232 link. No network neccessary. Supports keyboard and mouse.
There's a linux end, which runs on the Pi. The Pi then has a long serial cable, into your server room, which then connects to a 'teensy' (ie, https://www.pjrc.com/teensy). This will then run some Arduino code which emulates a keyboard and mouse.

## Why?! Why not just SSH/RDP?! ##

Because network latency sucks. Combine it with some uncompressed video extension (in my case, HDBaseT at 4k) for that nice cosy "I forgot it was in the other room" feeling.
Note that some HDMI forwarding devices also integrate USB-tunnelling functionality, which you can use if you prefer. Note that they often integrate virtual USB hubs, which they present to the host - which can confuse the KVM, as in my case, so I thought I'd roll my own.

## But RS232 sucks! It doesn't go far enough! ##
Get some RS232-to-RS485 converters. Sorted.

## I'm really really scared of the APT logging my keystrokes by observing the EM the RS232/RS485 link emits ##
lol. MAX_TINFOIL_HAT. Get some RS232-to-fibre converters.

# Building #

## Easy mode - use Docker ##

Just use Docker to build/run the container. Binaries will be in the container at /root/host (for the Linux-based host side) and /root/usbext-build/usbext.ino.hex (for the Teensy firmware).

```bash
docker build -t tmp .
docker run --name tmp tmp
docker cp tmp:/root/host ./host
docker cp tmp:/root/usbext-build/usbext.ino.hex .
```

If you have problems, take a look at the Jenkinsfile - the procedure in there should always work, so if it doesn't, raise a bug.

## PITA mode - do it yourself ##

First, install:

* The Arduno IDE: https://www.arduino.cc/en/Main/Software
* Teensy tools: https://www.pjrc.com/teensy/td_download.html

Open the .ino in the Arduino IDE, build it, and upload it to your Teensy.

Then, build the Linux host sw. You'll need to build from Linux.

* Run 'genkeys.sh', to generate keys-linux.h
* Run 'genkeys-teensy.sh', passing it your arduino install path, which will togenerate keys-teensy.h
* Compile it:
  gcc host.c -o host
  chmod +x host
* Run it:
  ./host

Sorted. If it doesn't work, take a look at the Dockerfile to work out what's wrong, and raise a bug either to fix the Dockerfile or update this documentation.
