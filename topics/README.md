# TOPICS

## Purpose

Each folder here holds the shareable content from one club meeting: notes, slides, handouts, diagrams, and homework. If you missed a meeting or want to review one, start with that meeting's `README.md`, which says what we covered and what each file is.

Meeting videos are **not** stored here, because GitHub isn't meant for large files. To watch a recording, go to [cdc2027.com](http://cdc2027.com) and click the YouTube icon.

## How the folders are organized

- Folder names are `YYYY-MM-DD-Short-Topic`, so they sort in meeting order.
- The **top level** of a meeting folder holds the files you'll use or refer to: handouts, notes, slides, and diagrams. Most are PDFs, which open in your browser straight from GitHub.
- The **`source/`** folder holds the Markdown and other originals that the top-level files were built from. You only need it if you want to fix or build on a document.

Some notes are AI-generated from the meeting recording, and they say so at the top. They're useful, but check them against the recording or ask in Zulip if something looks off.

## Meetings

| Date | Topic | Notes |
|---|---|---|
| 2026-09-19 | [Welcome and Internet Overview](2026-09-19-Welcome-and-Internet-Overview/) | Who we are, what makes a team member valuable, and how the Internet works |
| 2026-10-03 | [Building VMs on Proxmox](2026-10-03-Building-VMs-on-Proxmox/) | Removing old VMs, building new Ubuntu VMs, setting up Bitwarden |
| 2026-10-10 | [College, Connor, and the CLI](2026-10-10-College-Connor-and-the-CLI/) | Proxmox snapshots, Linux command line basics, "is college worth it?" |
| 2026-10-17 | [Basic Networking on Proxmox](2026-10-17-Basic-Networking-on-Proxmox/) | Static IP, gateway, DNS and proxy setup; `apt` basics |
| 2026-10-24 | [SSH and Networking](2026-10-24-SSH-and-Networking/) | Fixing the /24 mistake, installing the SSH server, first connections. No recording |
| 2026-10-31 | [SSH](2026-10-31-SSH/) | Proxmox performance settings, SSH users, protecting root |
| 2026-11-07 | [SSH Settings](2026-11-07-SSH-settings/) | Which `sshd_config` settings matter, and a partner lab to test them |

## Found a mistake?

Tell us in Zulip, or fix it yourself and open a pull request. Edit the file in `source/`, then either regenerate the PDF and include it in your PR, or say in the PR that you'd like it generated for you. Give the PDF a try first; it's good practice, and it's how the rest of the documents here were made.

To make a PDF that matches the ones already here: install the **Markdown PDF** extension (by yzane) in VS Code, open the `.md` file, press `Ctrl+Shift+P`, run **Markdown PDF: Export (pdf)**, and move the resulting PDF from `source/` up to the meeting folder, replacing the old one.

One gotcha: the export works on the editor tab that currently has focus, and that tab has to be VS Code's default text editor. The file can be open in other views at the same time, but if the focused tab is a Markdown preview or another Markdown editor (even VS Code's built-in one, or the Office Markdown Editor), the extension says there is no active editor. Click into the plain text tab and run the export again.
