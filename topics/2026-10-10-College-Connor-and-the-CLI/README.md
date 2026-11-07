# College, Connor, and the CLI

Linux command line basics, Proxmox snapshots, and a discussion about whether college is worth it for a tech career. The Proxmox cluster was slow and flaky all session.

## What we covered

- **Checking whether something is listening on a port** with `telnet <server> <port>`, which we used on the Proxmox web interface (port 8006).
- **Ubuntu install tips.** Take the defaults, say no to third-party software (the VMs have no Internet during install), and pick Chicago for the time zone.
- **Is college worth it?** The story of Connor, who went from answering support phones with a GED to being a software architect, plus the room's points for and against a degree: portability, employer requirements, cost, school name, code schools, and learning how to learn.
- **Proxmox snapshots.** How to take one (include RAM, no spaces in the name) and roll back. Most rollbacks failed with lock errors that day; that was the cluster, not you.
- **File managers vs. the terminal.** Nautilus, Dolphin and Thunar, and why admin work belongs in the terminal.
- **Command line basics.** The file system tree, `pwd`, `ls -al`, `cd`, `rm -rf`, `sudo -i`, absolute vs. relative paths, hidden dot files, Tab completion, what lives in `/etc` and `/usr`, and the `$` vs. `#` prompt.
- **The demo.** On a snapshotted VM, we deleted `/etc` and `/usr` as root and watched the system keep running until nobody could log in. That's what the snapshot is for.
- **Processes.** PIDs, `echo $$`, `ps auxf`, and why killing PID 1 is a fun thing to try only on a VM.

## Homework

- Get Ubuntu installed and working if you haven't.
- Practice the snapshot cycle: snapshot, change something, roll back, confirm the change is gone.
- Install `virt-viewer` on your own computer so you can use SPICE consoles.

## Files

| File | What it is |
|---|---|
| [Cyber-Defense-Meeting-Notes.pdf](Cyber-Defense-Meeting-Notes.pdf) | Full notes from the meeting, including every command we used and the homework |
| [Class-outline.pdf](Class-outline.pdf) | Chris's short plan for the session |
| [source/](source/) | Markdown originals of both documents |
