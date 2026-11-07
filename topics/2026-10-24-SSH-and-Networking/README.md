# SSH and Networking

We fixed last week's network problem, then installed and used the SSH server for the first time. **There is no recording of this meeting**, so the session guide is the full record.

## What we covered

1. Switch the VM display from SPICE to VirtIO GPU (and set display scaling to 200% if things look tiny).
2. The `ip` command, the loopback address, and what `/24` means.
3. What went wrong last week: the GUI saved our mask as `/0`, so each VM thought nothing else was on its network.
4. The correct network settings:
   - Address: `144.76.90.<your VM ID>`
   - Subnet mask: `255.255.255.0` (type the dotted form, not `/24`)
   - Gateway: `144.76.90.254`
   - DNS: `199.100.16.100`
5. `wget` a web page and why it looks unstyled when you open the saved file.
6. What SSH is, and how to tell the client is already installed.
7. Turning on the SSH service with `systemctl status`, `start` and `enable`.
8. Installing `openssh-server` on a VM that doesn't have it (`apt update` first).
9. Your first connection, the host key fingerprint, and `~/.ssh/known_hosts`.
10. Why port 22, and how to look it up in `/etc/services`.
11. Adding users in Settings → Users.
12. Connecting as a user, and using `hostname` and `ip a` to tell which machine you're on.
13. A diversion into `/etc/passwd`, `/etc/shadow`, `/etc/group`, and how to read `ls -l` permissions.
14. A look ahead at `sshd_config`, and why moving SSH off port 22 doesn't hide it.

## Homework

- Memorize the `ip` subcommands and what they do.
- Switch your display driver to VirtIO GPU.
- Install `virt-viewer` on your own computer, from your own console (not from inside an SSH session).

## Files

| File | What it is |
|---|---|
| [cyber-defense-2026-10-24-session-guide.pdf](cyber-defense-2026-10-24-session-guide.pdf) | The full write-up, one section per item above, with the commands and the reasoning |
| [cdc-2026-10-24-topic-summary.pdf](cdc-2026-10-24-topic-summary.pdf) | A one-page table of the same 14 items. Section numbers match the guide |
| [CDC-2026-10-24.md](CDC-2026-10-24.md) | Chris's working notes: the VM build steps, the `/24` fix, and the network and `apt` proxy settings |
| [source/](source/) | Markdown originals of the guide and the summary |
