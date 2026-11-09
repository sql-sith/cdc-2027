# SSH

Happy Hallowe'en. We tuned our VMs, got everyone's SSH server running, and talked about why protecting root is harder than it looks.

## What we covered

- **Proxmox performance.** Change the CPU type from `host` to `x86-64-v2-AES` (almost as capable, and the VM can migrate between hosts), make sure the display is VirtIO-GPU with 256 MB, install `virt-viewer` on your own computer, and set Ubuntu's display scaling to 200%. VMs stuck on a slow host can be migrated.
- **Fixing VM networking.** A VM with a `192.168.x.x` address is on the wrong network. Enter the static settings from [Oct 24](../2026-10-24-SSH-and-Networking/) without slash notation, and set the proxy for the desktop and for `apt`.
- **Installing and enabling the SSH server.** `sudo apt update`, `sudo apt install openssh-server`, then check `systemctl status ssh` for *active* (running now) and *enabled* (starts at boot).
- **Adding SSH users** in Settings → System → Users. Don't make them administrators; give them an easy password you can share with a lab partner.
- **Protecting root.** If an account that can `sudo` is compromised, so is root: the attacker can capture the password or swap in a fake `sudo`. Stronger controls move a second check outside the compromised session: separate admin accounts, hardware keys, short-lived certificates, password vaults.
- **Where SSH's settings live.** `/etc/ssh/sshd_config` plus the files in `/etc/ssh/sshd_config.d/`, read in alphabetical order, where the first value for a setting wins.
- **Recovering an incomplete Ubuntu install.** Detach the installer ISO from the virtual DVD drive, or reinstall if nothing was installed.

## Homework

1. **Finish your SSH setup.** `systemctl status ssh` shows running and enabled, you have created a user, and you can `ssh thatuser@<your VM's IP>`.
2. **Research `sshd_config`.** Find a resource that explains the most important settings and write them down for next week.
3. **Extra credit:** restrict a user so they can log in by SSH but not at the desktop or the console. Write up what you did and, if you can, submit it as a pull request under `docs/ssh-lockdown`.

The notes include rules for using AI on homework: try it yourself first, then ask the AI to explain rather than solve.

## Files

| File | What it is |
|---|---|
| [teaching-class-notes.pdf](teaching-class-notes.pdf) | Chris's class notes: the Proxmox changes, the SSH setup steps, and the homework |
| [2026-10-31-Club-Notes.pdf](2026-10-31-Club-Notes.pdf) | AI-generated notes from the meeting recording. Check them for accuracy |
| [source/](source/) | Markdown and Word originals of both documents |
