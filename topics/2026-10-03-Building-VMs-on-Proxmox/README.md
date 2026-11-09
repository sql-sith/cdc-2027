# Building VMs on Proxmox

"The day Proxmox stood still." We deleted last year's VMs, built new ones, and set up password managers. Proxmox did not enjoy the volume of requests.

## What we covered

- **Removing old VMs** in the Proxmox web interface, including the two checkboxes that purge the VM from job configurations and destroy its disks.
- **Building a new Ubuntu VM**, tab by tab: resource pool `School-14`, the `<firstname>-<OS>-<index>` naming scheme (`chris-ubuntu-01`), the Ubuntu 24.04.3 Desktop ISO, SSD emulation and Discard on the disk, 4 GiB of memory, and default networking.
- **What went wrong.** VM removals took forever and blocked the VM builds behind them (lock contention). The Bitwarden part went fine.
- **Bitwarden.** Everyone created a free account and added the browser extension, so we have somewhere safe to keep passwords this season.

## Settings we changed later

The deck shows the settings we used that day. Later meetings changed some of them, so use these instead:

| Setting | Deck says | Use now | Changed on |
|---|---|---|---|
| Display | SPICE | VirtIO-GPU, 256 MB | [Oct 24](../2026-10-24-SSH-and-Networking/) |
| CPU type | host | x86-64-v2-AES | [Oct 31](../2026-10-31-SSH/) |
| CPU layout | 1 socket, 4 cores | 3 sockets, 1 core each, CPU limit 8 | [Oct 17](../2026-10-17-Basic-Networking-on-Proxmox/) |

## Files

| File | What it is |
|---|---|
| [2026-10-03-Buiding-VMs-on-Proxmox.pptx](2026-10-03-Buiding-VMs-on-Proxmox.pptx) | The slides, with step-by-step instructions for removing a VM, building one, and setting up Bitwarden |
