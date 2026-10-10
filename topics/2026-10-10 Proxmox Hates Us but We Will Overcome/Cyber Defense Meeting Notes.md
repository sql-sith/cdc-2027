# Cyber Defense Meeting Notes

Oct 10, 2026 · @Chris Leonard

| attending | huwebee                                                                  |
| --------- | ------------------------------------------------------------------------ |
| in person | Hans; Isaiah and Josh; Naomi and Jon; Kizek, Koa, and Angela; and Isaac  |
| remotely  | Karl                                                                     |

## Summary

We learned Linux command line basics, took and (tried to) roll back Proxmox snapshots, and talked about whether college is worth it for a tech career. Our remote Proxmox cluster was slow and flaky all session: some VMs showed as stopped, the web console connected only sporadically, and snapshot rollbacks failed with lock errors. Coach Chris posted in the Discord support channel, and the admins will need to look at the cluster. We did not get to the VM-breaking race; that moves to next week.

## Troubleshooting: is anything listening on a port?

The Proxmox web interface runs on port 8006. One possible cause of connection trouble is that nothing is listening on that port, so we tested it with telnet.

1. Type `telnet`, then the server name, then the port number, separated by spaces.

   ```powershell
   telnet playground.iseage.org 8006
   ```

2. Read the result:

   - An immediate "could not open connection" style failure means you could not reach anything on that port.&#32;
   - A timeout also indicates that you could not reach anything on that port. Timeouts may take 20 or more seconds.
   - Anything else (it connects, or the screen goes blank waiting) means something is listening. That counts as a success.&#32;
   - After connecting, if you don't get kicked off the server quickly, you can a) use Ctrl-C to exit telnet, b) kill telnet, or c) use the escape sequence Ctrl-RightSquareBracket (`` ` ```^``\]`` ` ``)``,`` ``w``h``i``c``h`` ``w``il``l`` ``t``a``k``ey``o``ut``o`` ``a`` ``m``e``n``uw``h``e``r``ey``o``uc``a``nt``y``p``e`` `` ` ``c``l``o``s``e`` ` `` ``a``n``d`` ``t``h``e``n`` ``` ` ```q``ui```t`.``&#32;
3. Don't forget the port number. Without it, telnet tries the default telnet port (23) and fails, which tells you nothing about port 8006.

## Ubuntu install tips

- Take the defaults on most screens.
- When asked about installing third-party software, say no. Our VMs have no internet access, so the installer hangs trying to download it.
- On the time zone screen, click near Chicago on the map or pick it from the drop-down.

## Discussion: is college worth it?

There is no single answer, but a degree opens doors and a no-degree path demands unusual drive. We used a real example to dig in.

### Jody's career path (shared with his permission; name changed)

Jody is a GoDaddy colleague of Coach Chris who is now in the final interviews for a job building AI safety guardrails.

- Dropped out of high school and got his GED.
- Started answering support phones at GoDaddy, moved through a few teams, and landed in hosting, then advanced hosting support (the people who get escalations the phone team can't fix).
- Moved to server support, fixing customers' websites.
- Taught himself to code by looking for problems nobody knew they had. He says that is still what he does.
- Went on a "safari" (a temporary stint working on another team) with a Domains team and stayed. He is now an architect in the domains space.

His advice, in short:

- Whether to go to college depends on the person. School was a poor learning environment for him.
- At a minimum you need an extreme urge for learning, self-improvement, and problem solving, plus tolerance for work that is uncomfortable, ambiguous, and unclear, and the persistence to keep trying until you succeed.
- Coding camps and self-learning are options, but the path won't be easy.
- Learning from real problems instead of theory made him very good at his job.
- He knows only one other person from the phones who became an engineer without a degree.
- People without degrees may not reach the highest levels of engineering leadership, but most people with degrees don't either.
- Luck matters on any path. He only got the chance to show his skills because someone asked about a safari for him.

### Points from the room

- **A degree is portable.** Certifications tend to keep you in one specialty; a bachelor's in IT carries weight across more of the field.
- **Many companies still require a four-year degree**, even though people often say they used little of it on the job. GoDaddy job postings say "bachelor's degree or equivalent experience," which raises the question of how you get the experience.
- **Credentials get you the interview; you have to win the interview.** This matters most when you're young.
- Spending $100,000 on a degree that will never pay it back is hard to justify.
- **School name only matters for top programs** because of the projects students get to do there, the partnerships they have with top industry and research institiutions, and programs that have long histories of producing strong graduates. Examples are Carnegie Mellon research opportunities, Georgia Tech's engineering departments, and many programs at schools like Stanford. For other schools, whether traditional, online, or remote, the degree works but the school matters less.
- **Code schools are another route.** One parent's company has hired about three developers from a one-year code school, without degrees, because they could do the work and came recommended.
- **Competition can be stiff.** Coach Chris's son Gabe, with a double major in math and computer science, worked on Google teams where colleagues had master's degrees and PhDs, which made review time tough.
- **Famous dropouts are the exception.** Bill Gates and Steve Jobs are memorable precisely because they're rare. Dropping out won't make you the next one.
- **College can teach you how to learn.** Liberal arts classes like a German translation seminar teach you to think about tradeoffs (translate for meaning or word for word?) that apply far beyond the class.

## Proxmox snapshots

A snapshot is a point-in-time copy of your whole VM. Take one before you experiment, and you can roll back to it no matter how badly you break things.

### Take a snapshot

1. In the Proxmox web interface, click your VM in the left-hand list.
2. In the middle panel, click **Snapshots**.
3. Click **Take Snapshot** near the top.
4. Give it an obvious name, such as `restore_this_one`. Names allow only letters, numbers, and underscores (no spaces or hyphens).
5. Make sure **Include RAM** is checked. It should be by default, but verify it.
6. Click **Take Snapshot** and wait for the task to finish. Check the task log at the bottom of the screen for "TASK OK."

The first snapshot is large because it saves your RAM contents and creates a new disk image (qcow2 file) to hold later changes. Later snapshots build on it and are smaller.

### Rollback to a snapshot

1. Go to **Snapshots** for your VM.
2. Click the snapshot you want to go back to.
3. Click **Rollback**, and say yes to the scary warning.
4. Wait for the task to finish, then check that whatever you changed is gone.

Today most rollbacks failed with lock errors (the VM stayed locked). That looks like a cluster problem, not something you did, and the admins are looking for ways to fix the problem.

### Snapshot cautions

- Snapshots take real disk space. At competition we can't take one after every change, so be judicious about when you take them.
- Keep the snapshot tree simple and clearly named. A long tree of snapshots gets confusing fast.
- If the console shows "Booting from hard disk" and hangs after an install, try stopping the VM, removing the install DVD/ISO under Hardware, and starting it again. If that fails, the install may not have finished and you may need to reinstall.
- If the web page still shows a VM as running after you stop it, reload the page.

## Linux file managers, and why we switched to the terminal

We started breaking things with a graphical file manager, but it couldn't touch system files, so we moved to the terminal.

| File manager                   | Desktop it comes with   | Notes                                                      |
| ------------------------------ | ----------------------- | ---------------------------------------------------------- |
| Nautilus (shows up as "Files") | GNOME, Ubuntu's default | Had Wayland display errors in our VMs                      |
| Dolphin                        | KDE                     | Pulls in a huge number of Qt dependencies (hundreds of MB) |
| Thunar                         | Xfce                    | Old, lightweight, few dependencies; ran fine under X       |

What we ran into:

- Pressing Delete in a file manager may only move files to the trash. Shift+Delete deletes them outright.
- Hidden files (names starting with a dot) don't show by default.
- System files gave "permission denied" because we were a normal user.
- Running a graphical app as root causes X authority and display problems. For admin work, use the terminal.

## Linux command line basics

**Only do the destructive parts of this on a VM you have snapshotted. Never on your own computer.**

To open a terminal in Ubuntu, click on the desktop and press **Ctrl+Alt+T**.

### The file system is a tree

Like Windows, Linux folders form a hierarchy. Windows starts at a drive like `C:\`; Linux starts at `/`, called the **root** directory. Everything hangs off it:

```plain
/
├── etc          configuration files
├── home
│   └── chris    your home directory
├── root         the root user's home directory
├── usr          installed programs and libraries
└── ...
```

Your terminal is always "in" one folder, called the working directory. A new terminal starts in your home directory, such as `/home/chris`.

### Commands we used

| Command                  | What it does                                                                           |
| ------------------------ | -------------------------------------------------------------------------------------- |
| `pwd`                    | Print working directory: shows which folder you are in                                 |
| `ls`                     | List what's in a folder, in compact form                                               |
| `ls -al`                 | `-a` = all, including hidden files; `-l` = long listing with details                   |
| `ls -al --color=never`   | Same, without colors that are hard to read on some screens                             |
| `cd <folder>`            | Change directory                                                                       |
| `rm -rf <thing>`         | Remove:`-r` = recursive (folders and everything inside), `-f` = force (no prompts)     |
| `sudo -i`                | Become root (the admin user), asking for your own password                             |
| `echo $$`                | Show the process ID (PID) of your current shell                                        |
| `ps auxf`                | List running processes as a tree                                                       |

Flags can usually be combined (`-al`, `-rf`) and the order doesn't matter (`-rf` = `-fr`).

### Special names and paths

- `.` means **this** directory; `..` means the **parent** (one level up). Both exist in every directory.
- To find the parent, chop the last part off the path: the parent of `/home/chris` is `/home`, and the parent of `/home` is `/`.
- `..` at the root is still the root. There is nothing above `/`.
- Files and folders whose names start with a dot (like `.bashrc`) are **hidden**. `ls` and `*` skip them unless you ask: `ls -a` shows them, and `.*` matches them.

Three ways to name the same folder when you're already in `/etc`:

| Style             | Example          | Meaning                                 |
| ----------------- | ---------------- | --------------------------------------- |
| Absolute path     | `/etc/systemd`   | Starts at the root, works from anywhere |
| Relative with dot | `./systemd`      | Explicitly "in this folder"             |
| Relative          | `systemd`        | Implicitly in this folder               |

Coach Chris prefers the `./` form for destructive commands: explicit is better than implicit, and it forces you to stare at exactly what you're about to delete.

### Tab completion

Type the start of a name and press **Tab**. If it's unique, the shell finishes it. If not, press **Tab twice** to see the choices. It saves a huge amount of typing and avoids typos, yet many students never learn it. You love the Tab key.

### Important places in /etc and /usr

- `/etc` holds configuration. If you're wondering where a program keeps its settings, the answer is in `/etc` about 70% of the time.
  - `passwd`: the list of user accounts
  - `shadow`: users and their hashed passwords
  - files ending in `.conf`: configuration files
  - files with `cron` in the name: cron, the job scheduler
  - `systemd/`: controls how the system starts up and runs services
- `/usr` holds installed programs and libraries. Modern distros merged older folders like `/bin` and `/lib` into it. Because it rarely changes, it can live on a separate, read-only disk, which is the basis of "immutable" operating systems like Android.

### Permissions and sudo

As a normal user, `rm -rf systemd` in `/etc` failed with a wall of "permission denied" errors. `-f` forces past prompts, not past permissions.

On Ubuntu the root account has no password, so you can't log in as root. Instead, the first user created is an administrator who can run `sudo -i` to become root.

- The prompt changes from `$` to `#` when you're root. Treat `#` as a warning.
- `sudo -i` moves you to root's home folder, `/root`, so `cd` back to where you were working.

### What we did (the demo)

```bash
pwd                      # /home/chris
ls -al                   # only hidden dot files were left
rm -rf *                 # removed nothing: * skips hidden files
rm -rf .*                # removed the hidden files; rm refuses to remove . and ..
cd /etc
rm -rf systemd           # permission denied as a normal user
sudo -i                  # become root (prompt ends in #)
cd /etc
rm -rf systemd           # worked
cd ..
rm -rf ./etc             # deleted all configuration
rm -rf usr               # deleted programs in /usr
```

Afterward, nobody could log in, because the `passwd` and `shadow` files were gone. That's the point where you roll back your snapshot.

A side note on deleted files: Linux keeps a deleted file alive as long as a running program still has it open. That's why the VM kept running after we deleted `/etc`, and it's how Coach Chris once rescued an Oracle test server whose database files he had deleted while it was running.

### Processes

Every running program has a process ID (PID). `echo $$` shows your shell's PID (the `$` starts a variable, and the variable here is named `$`). PID 1 is the first process (`systemd` on Ubuntu), and every other process descends from it. `ps auxf` shows that process tree. For a simplified format of the process tree, you can use `ps -ejH`. Killing PID 1 crashes the whole system, which makes it a fun thing to try on a snapshotted VM.

## Homework

- [ ] If you're still installing, get Ubuntu installed and working.
- [ ] Leave your VM running for now (unusual for us) until we figure out what's wrong with the cluster.
- [ ] Practice the snapshot cycle: take a snapshot, make a change you can check, roll back, and confirm the change is gone. The change can be as simple as creating and saving a file, or as dramatic as deleting `/etc` or killing PID 1.
- [ ] Ensure that you have `virtviewer` installed. This lets you use SPICE terminals.

On Windows, run this command to install virt-viewer&#32;

```powershell
winget install --Id RedHat.VirtViewer --Source winget --Exact 
```

On Ubuntu, run these two commands:&#32;

```bash
sudo apt-update
sudo apt-upgrade virt-viewer 
```
