# Basic Networking on Proxmox

Getting our VMs onto the club network by hand: static IP address, gateway, DNS, and the proxy that stands between us and the Internet.

## What we covered

- **Housekeeping.** Change your VM's CPU layout to 3 sockets, 1 core each, with a CPU limit of 8. Keep the type as `host` for now (that changed again on [Oct 31](../2026-10-31-SSH/)).
- **Pre-flight checklist.** Empty the virtual DVD drive, make sure the VM can shut down and start again, and take a snapshot before touching the network.
- **Pop quiz.** Which server hands out IP addresses (DHCP)? Which one turns names into addresses (DNS)? Which device sends packets to other networks (the router)?
- **Static networking in the Ubuntu GUI.** Why DHCP won't give us the address we need, and how to enter the address, mask, gateway, DNS and proxy by hand. The corrected values are in the [Oct 24 session guide](../2026-10-24-SSH-and-Networking/), after we found out what went wrong with the mask.
- **Why there's a proxy at all.** The playground and competition networks are isolated from the real Internet so nobody can accidentally attack a real company. The proxy is the only way out.
- **Proxy settings for the command line.** `/etc/apt/apt.conf` for `apt` and `/etc/wgetrc` for `wget`; the syntax has to be exact.
- **`apt` basics.** `apt update` vs. `apt upgrade`, `apt search`, `apt install`, and the demo with `cowsay` and `fortune` (which lives in `/usr/games`, so it needed a `PATH` change).
- **Diagnostics.** `ip a`, `ip route`, `ping` the gateway, `telnet` to the proxy port, and `whois` to find who owns an address block.
- **Open question.** NetworkManager and Netplan were fighting over the settings, so the manual configuration didn't always survive a reboot.

## Files

| File | What it is |
|---|---|
| [2026-10-17-Basic-Networking-on-Proxmox.pdf](2026-10-17-Basic-Networking-on-Proxmox.pdf) | Chris's plan for the session: housekeeping, checklist, quiz, and the networking steps |
| [meeting-notes-facilitator.pdf](meeting-notes-facilitator.pdf) | AI-generated notes from the Teams recording. Only Chris's laptop mic was on, so the notes credit almost everything to Chris. Check anything surprising against the recording |
| [source/](source/) | Markdown originals of both documents |
