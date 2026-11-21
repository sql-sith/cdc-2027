# Student Guide: Package Management on Ubuntu (apt and snap)

Nov 21, 2026 · @Chris Leonard

## Before you start

- **Paste into the terminal** with Ctrl+Shift+V (not Ctrl+V). Then press Enter.
- **Anything after a `#` is a comment.** It explains the line. The terminal ignores it, so it is safe to paste.
- **`sudo` means "run this as the administrator".** It asks for your password. Nothing shows while you type it. That is normal.
- **Blocks marked "paste the whole block"** are safe to paste in one go. Read the comments so you know what each line did.
- **Lines marked "type this one yourself"** ask you a question or take over the screen. Run them one at a time and read what they say before you answer.
- If the screen looks stuck with a `:` at the bottom, you are in a pager. Press `q`.

Run this once now so the catalog is fresh and sudo remembers your password for a while:

```bash
sudo apt update
```

## Part 1 - update vs upgrade

`update` downloads the new catalog of what is available. It changes no software. `upgrade` installs the newer versions the catalog lists. Always update first.

Paste the whole block:

```bash
sudo apt update                 # refresh the catalog (changes no software)
apt list --upgradable           # list what could be upgraded
```

Type this one yourself. Read the summary line (how many upgraded, how big the download is), then **answer n**:

```bash
sudo apt upgrade
```

## Part 2 - install, remove, purge

Check before you install. Paste the whole block:

```bash
apt policy figlet                           # "Installed: (none)" means it is not installed
sudo apt install -y figlet cmatrix lolcat   # install three packages at once
apt policy figlet                           # now it shows a version number
```

Type these yourself, one at a time. Use your own name:

```bash
figlet YOURNAME
figlet YOURNAME | lolcat
cmatrix                         # press q to quit
```

Now remove one. Type these yourself, **read the list each one shows**, then answer y:

```bash
sudo apt remove lolcat          # removes the program
sudo apt autoremove             # removes helper packages nothing needs anymore
```

### remove vs purge

Paste the whole block:

```bash
sudo apt install -y vsftpd      # an FTP file server, with a config file
systemctl is-active vsftpd      # "active" - a server starts the moment it is installed
ls -l /etc/vsftpd.conf          # its config file
```

Type this one yourself and answer y:

```bash
sudo apt remove vsftpd
```

Paste the whole block:

```bash
ls -l /etc/vsftpd.conf          # still there - remove left the config behind
dpkg -l vsftpd                  # "rc" at the left = removed, config remains
```

Type this one yourself and answer y:

```bash
sudo apt purge vsftpd
```

Check that it is really gone:

```bash
ls -l /etc/vsftpd.conf          # No such file or directory
```

**Remember:** `remove` leaves config files behind. `purge` deletes them too. In competition, purge.

## Part 3 - what is installed?

The `|` (pipe) sends the output of the left command into the right one. `grep` keeps only the lines that match.

Paste the whole block:

```bash
dpkg -l | grep -i cmatrix       # "ii" at the left = installed
dpkg -l | wc -l                 # roughly how many packages are on this box
apt show cmatrix                # description, version, size, what it depends on
which cmatrix                   # where the program file lives
dpkg -S /usr/bin/cmatrix        # which package owns that file
```

The program name is not always the package name. `which` then `dpkg -S` gets you from a program to its package.

## Part 4 - spotting software that should not be there

In competition, the README lists the software the company needs. Hacking tools, games, and servers that are not on that list cost you points.

Paste the whole block:

```bash
dpkg -l | grep -Ei 'nmap|john|hydra|crack|game|ftp|telnet'   # search for suspicious names
dpkg -l | grep '^rc'            # packages that were removed but not purged
ls /usr/games                   # games usually live here
```

**A grep hit is a lead, not a verdict.** Run `apt show NAME` before you purge anything. For example, `libcrack2` matches "crack" but it checks password strength. You need it.

## Part 5 - snap: the second place software lives

snap is a separate app store with its own list. `apt` and `dpkg -l` cannot see snaps. You must check both.

Paste the whole block:

```bash
snap list                               # what is installed as a snap
snap find hello                         # search the snap store
sudo snap install hello-world           # install a snap
hello-world                             # run it
dpkg -l | grep hello-world              # nothing - dpkg cannot see snaps
sudo snap remove --purge hello-world    # remove it completely
snap list                               # hello-world is gone
```

Do not remove these snaps: `snapd`, anything starting with `core`, `bare`, anything starting with `gnome-`, `gtk-common-themes`, `snap-store`, `firefox`.

## Challenge - clean the box

### Setup (wait for the coach)

When the coach says go, paste this one long line. It plays the part of a careless admin. Then hands off the keyboard while it runs.

```bash
sudo apt update && sudo apt install -y nmap john hydra vsftpd ninvaders gnome-mines && sudo snap install nmap && sudo sed -i 's/"1"/"0"/' /etc/apt/apt.conf.d/20auto-upgrades && clear || echo "PLANT FAILED - call the coach"
```

If you see "PLANT FAILED", raise your hand.

Next, install ONE secret extra. Pick one game from this list, tell nobody which one, then clear the screen:

```bash
sudo apt install -y bastet      # or pick one: moon-buggy, nsnake, pacman4console
clear
```

Now swap seats when the coach says so.

### Your mission

You are the new admin on this box. Company policy: **no hacking tools, no games, no file servers.**

README: `figlet` and `cmatrix` are company-required. Leave them alone.

1. Find all unauthorized software. The last admin added 6 apt packages and 1 snap, plus 1 secret extra. One of them shows up in the app grid - find its package name. Games that were already on the box are bonus finds.
2. Before you remove anything, prove it is installed and check what it is: `apt policy NAME`, `apt show NAME`, `snap list`.
3. Remove them and leave no config files behind.
4. Clean up packages that are no longer needed.
5. Automatic security updates have been turned off. Turn them back on.
6. Last: run `sudo apt update`, then `sudo apt upgrade`, and read the summary. Tell a scorer how many packages would upgrade. Answer y only if the coach says there is time, and do not shut down until it finishes.

Stuck? Everything you need is in Parts 2 to 5 and the cheat sheet below. Ask a helper for a hint.

## Cheat sheet

| Command | What it does |
| --- | --- |
| `sudo apt update` | Refresh the catalog. Changes no software. |
| `sudo apt upgrade` | Install newer versions of what you have. |
| `apt list --upgradable` | Show what could be upgraded. |
| `apt policy NAME` | Is it installed? Which version is on offer? |
| `apt show NAME` | What is this package? |
| `sudo apt install NAME` | Install a package. |
| `sudo apt remove NAME` | Remove the program, keep its config. |
| `sudo apt purge NAME` | Remove the program and its config. |
| `sudo apt autoremove --purge` | Remove leftover helper packages and their config. |
| \`dpkg -l | grep WORD\` |
| `which PROGRAM` | Where does this program live? |
| `dpkg -S /path/to/file` | Which package owns this file? |
| `ls /usr/games` | Where games usually live. |
| `snap list` | Installed snaps. |
| `sudo snap install NAME` | Install a snap. |
| `sudo snap remove --purge NAME` | Remove a snap completely. |
| `hash -r` | Make the terminal forget where a removed program used to be. |

## Check yourself

1. What does `apt update` change on your system?
2. What does `purge` do that `remove` does not?
3. You purged nmap with apt, but `nmap` still runs. Where else do you look?

## If you finish early

Second-years: find out who installed what on your box, and when. Be ready to report it in 30 seconds.

Paste the whole block:

```bash
grep -h Commandline /var/log/apt/history.log    # every apt command that was run
apt-mark showhold                               # packages blocked from upgrading
apt-mark showmanual | head -30                  # packages someone asked for by name
apt-cache policy nmap                           # which repo a package comes from
ls /etc/apt/sources.list.d/                     # extra repos - anything odd here is suspect
apt-config dump | grep Periodic                 # automatic update settings
snap changes                                    # recent snap installs and removals
```

Things to try one at a time:

```bash
less /var/log/apt/history.log                   # full install history; press q to quit
sudo apt-mark hold openssl                      # block a package from upgrading
apt-mark showhold                               # see it listed
sudo apt-mark unhold openssl                    # undo it - always undo it
```

A held package silently blocks security updates. That is a real competition trick, so always check `apt-mark showhold`.
