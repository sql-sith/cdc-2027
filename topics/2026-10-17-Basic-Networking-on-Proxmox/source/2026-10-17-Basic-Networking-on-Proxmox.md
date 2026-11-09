# Meeting 2026-10-17

## Housekeeping

When you have time, shutdown your VMs and switch their CPU configuration to have

* **Sockets**: 3
* **Cores**: 1
* **VCPUs**: 3
* **CPU limit**: 8

Leave **Type** as **host**.

## Pre-flight checklist

1. Make sure your DVD is empty
2. Make sure you can shut down and start again (not just restart)
3. Shutdown your VM and take a snapshot.
    * Give it a descriptive name. I called mine *before-adding-networking-via-gui*
    * Remember, the snapshot name can't have spaces in it
4. Start your VM again and log on

## Pop quiz

1. Here are questions for students that didn't know all of this at our first meeting. Use the week 1 picture in our repo if you need to.

   * What kind of server will give us an IP address?
   * What kind of server will tell us the IP address of other computers?
   * What kind of device will send our network packets to other networks (such as the Internet)?

2. We are going to tell Ubuntu how to find each of these servers and devices now. We will also introduce another device, called a Proxy.

## Adding basic networking in Ubuntu

1. On the Ubuntu desktop, click 