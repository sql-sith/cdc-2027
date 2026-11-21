# Lesson Plan: Package Management on Ubuntu (apt and snap)

Nov 21, 2026 · @Chris Leonard

## Assumptions and objectives

**Assumptions (check these):** 60-minute session. Each student has their own Ubuntu Desktop VM (22.04 or 24.04), sudo rights, and internet access. Beginners can open a terminal and maybe use `cd` and `ls`. The terminal is the main skill; the GUI gets 4 minutes.

By the end, a student can:

1. Explain the difference between `apt update` and `apt upgrade` and run both.
2. Install, remove, and purge a package, and say why purge matters.
3. Find out whether a program is installed (apt or snap) and which package owns it.
4. Find and remove unauthorized software from a box and bring it up to date.

## Pre-class checklist (2 min, on one student VM)

- [ ] `sudo apt update` finishes with no errors (network and repos OK, no lock held).
- [ ] `apt list --upgradable 2>/dev/null | wc -l` - note the count. Over about 100 means a full upgrade will not finish in class.
- [ ] `snap version` works, and `snap info nmap` returns a result (the challenge plants the nmap snap).
- [ ] `apt-cache policy figlet cmatrix lolcat vsftpd nmap john hydra ninvaders gnome-mines bastet moon-buggy nsnake pacman4console` shows a Candidate for each. Note any already installed - students will discover the same thing.
- [ ] `software-properties-gtk` opens (Software & Updates). Close any Software Updater popup so it does not hold the apt lock.
- [ ] Share the student guide with the class so they can copy and paste from it. It has the plant command and the task list, but no hints and no answer key.

## Agenda (60 min)

**Hard rule: the plant starts at 0:36 no matter where you are.** Cut from Blocks 4-6 to protect the challenge.

| Clock | Block | The one thing they type |
| --- | --- | --- |
| 0:00 | 1 - What is a package | `sudo apt update` as they sit down |
| 0:03 | 2 - update vs upgrade | `sudo apt upgrade`, read, answer n |
| 0:09 | 3 - install, remove, purge | `figlet YOURNAME` (catch-up gate) |
| 0:19 | 4 - What is installed | `dpkg -S /usr/bin/cmatrix` |
| 0:26 | 5 - Competition talk | \`dpkg -l |
| 0:29 | 6 - snap | `snap list` |
| 0:36 | 7 - GUI (projector only) | the plant command, then hands off |
| 0:40 | Challenge | clean the box |
| 0:56 | Exit check | 3 questions |

**Room rules:** A broken VM pairs with a neighbor - nobody debugs during class. Second-years get the stretch list at 0:09; as helpers they answer only with a question or a command to try, and never touch a beginner's keyboard.

### Block 1 - What is a package? (0:00-0:03)

As students sit down, everyone runs `sudo apt update`. This clears the boot-time lock before Block 2 needs it.

Say: "A **repository** is an app store warehouse run by Ubuntu. A **package** is one boxed product in it - a program plus its files and a list of other boxes it needs. **apt** is the delivery service. Your VM keeps a local copy of the warehouse catalog. You just refreshed it."

### Block 2 - update vs upgrade (0:03-0:09)

Say: "`update` downloads the new catalog. It changes no software. `upgrade` installs the newer versions the catalog lists. Always update first."

```bash
sudo apt update                 # refresh the catalog
apt list --upgradable           # what could be upgraded (no sudo needed)
sudo apt upgrade                # READ the summary, then answer n
```

- At the prompt, have them read the "N upgraded, N newly installed, N to remove" line and the download size, then **press n**.
- If a VM is already up to date there is no prompt. Fine.
- Ask: "You ran `apt upgrade` without `apt update`. What happens?" (It works from a stale catalog and misses new fixes.)

### Block 3 - install, remove, purge, autoremove (0:09-0:19)

Say: "Before you install anything, check whether it is already there. Same habit you need in competition."

```bash
apt policy figlet               # "Installed: (none)" = not installed
sudo apt install -y figlet cmatrix lolcat
figlet YOURNAME                 # big ASCII letters
figlet YOURNAME | lolcat        # same, in rainbow
cmatrix                         # Matrix rain; press q to quit
sudo apt remove lolcat          # READ what it will do, then y
sudo apt autoremove             # READ the list (ruby helpers), then y
sudo apt install -y vsftpd      # an FTP server, has a config file
sudo apt remove vsftpd
ls -l /etc/vsftpd.conf          # still there
dpkg -l vsftpd                  # "rc" at the left = removed, config remains
sudo apt purge vsftpd
ls -l /etc/vsftpd.conf          # No such file
```

- **Catch-up gate:** `figlet YOURNAME` is readable from across the room. Do not move on until every screen shows a name.
- `-y` is on installs only. On remove, purge, and autoremove they read the list and type y themselves.
- Say: "`remove` leaves config behind. `purge` removes config too. In competition, purge - leftover config can hold a backdoor setting and comes back to life if the package is reinstalled."
- Say: "`autoremove` cleans up helper packages nothing needs anymore. Always read the list first."
- If a student already had one of these: good. Ask how they could tell.

### Block 4 - What is installed? (0:19-0:26)

```bash
dpkg -l | grep -i cmatrix               # "ii" at the left = installed
dpkg -l | wc -l                         # roughly how many packages are on this box
apt show cmatrix                        # description, version, size, dependencies
which cmatrix                           # where the program lives
dpkg -S /usr/bin/cmatrix                # which package owns that file
```

- Explain the pipe once, here: "`|` sends the output of the left command into the right one. `grep` keeps only matching lines."
- `dpkg -l` with no pipe opens a pager. Press `q` to quit.
- Say: "The program name is not always the package name. `which` then `dpkg -S` gets you from one to the other."
- Check: everyone can answer "which package does `/usr/bin/cmatrix` belong to?"

### Block 5 - Competition talk (0:26-0:29)

Mostly talk. Say: "The README names the software the 'company' needs. Anything else that is a hacking tool, a game, or an unneeded server costs you points. If the README says it is required, leave it alone."

```bash
dpkg -l | grep -Ei 'nmap|john|hydra|crack|game|ftp|telnet'
dpkg -l | grep '^rc'                    # anything removed but not purged
ls /usr/games                           # games usually live here
```

- Say: "**A grep hit is a lead, not a verdict.** Run `apt show NAME` before you purge." The `crack` search will likely show `libcrack2`, which is password-strength checking they need.
- Names to recognize: nmap, john, hydra, ophcrack, aircrack-ng, wireshark, netcat variants, games, and servers the README does not ask for (vsftpd, telnetd, apache2, samba).
- Say: "Keyword grep only finds what you think to search for. `ls /usr/games` plus `dpkg -S` on what you find catches the rest."

### Block 6 - snap: the second place software lives (0:29-0:36)

Say: "snap is a separate app store with its own list. apt does not see snaps. `dpkg -l` does not see snaps. On a competition box you must check both. Firefox on Ubuntu is a snap."

```bash
snap list                               # what is already here as a snap?
snap find hello                         # search the store
sudo snap install hello-world
hello-world                             # prints Hello World!
dpkg -l | grep hello-world              # nothing - apt/dpkg cannot see it
sudo snap remove --purge hello-world    # --purge skips saving a data snapshot
```

- Do not remove base snaps: `snapd`, `core20/22/24`, `bare`, `gnome-*`, `gtk-common-themes`, `snap-store`, `firefox` (unless the README says so).
- Mention only: `sudo snap refresh` updates all snaps. Do not run it now - it is large.

### Block 7 - The GUI side, projector only (0:36-0:40)

**First, students paste the plant command from the Challenge section, then hands off keyboards.** The installs run while they watch you.

Say: "Every one of these is a front end for commands you just typed."

| GUI app | Start it with | It is really |
| --- | --- | --- |
| Software Updater | app grid, or `update-manager` | `apt update` + `apt upgrade` with a progress bar |
| Software & Updates | app grid, or `software-properties-gtk` | editor for the apt sources files and the automatic-update settings |
| Store: Ubuntu Software (22.04) / App Center (24.04) | app grid, or `snap-store` | mostly `snap find/install/remove`; can also show deb packages |

- **Software & Updates:** the "Other Software" tab lists extra repos and PPAs - anything odd here is suspect. The "Updates" tab is a **competition item**: "Automatically check for updates" set to Daily, and security updates set to "Download and install automatically". Then show that the GUI just wrote a file: `apt-config dump | grep Periodic` (the Update-Package-Lists and Unattended-Upgrade values are "1"). Students do this themselves in the challenge.
- **Store:** open the installed list and compare with `snap list`. Say: "The store is not a complete list of what is on the box. The terminal is the truth: `dpkg -l` and `snap list`, both."
- Do not click Install or Update in any of these - they take the apt lock.

## Challenge: clean the box (0:40-0:56)

**Plant - students paste this at 0:36, at the start of Block 7:**

```bash
sudo apt update && sudo apt install -y nmap john hydra vsftpd ninvaders gnome-mines && sudo snap install nmap && sudo sed -i 's/"1"/"0"/' /etc/apt/apt.conf.d/20auto-upgrades && clear || echo "PLANT FAILED - call the coach"
```

The plant also turns automatic updates off, so task 5 is real. If the nmap snap is the part that fails, run `sudo snap install john-the-ripper` by hand.

**Secret extra (0:40):** each student installs ONE game of their choice from this list, tells nobody, runs `clear`, then everyone swaps seats.

```bash
sudo apt install -y bastet          # or: moon-buggy, nsnake, pacman4console
```

Short on time? Skip the secret extra and the swap; students clean their own box.

**Task list (give to students):**

You are the new admin. Company policy: no hacking tools, no games, no file servers. README: `figlet` and `cmatrix` are company-required. Leave them alone.

1. Find all unauthorized software. The last admin added 6 apt packages and 1 snap, plus 1 secret extra. One shows up in the app grid - find its package name. Games that were already on the box are bonus finds.
2. Before you remove anything, prove it is installed and check what it is (`apt policy NAME`, `apt show NAME`, `snap list`).
3. Remove them and leave no config files behind.
4. Clean up packages that are no longer needed.
5. Automatic security updates have been turned off. Turn them back on.
6. Last: run `sudo apt update`, then `sudo apt upgrade`, and read the summary. Tell a scorer how many packages would upgrade. Answer y only if there are 8+ minutes left, and do not shut down until it finishes.

**Hints, one at a time as needed:**

- `ls /usr/games` then `dpkg -S /usr/games/NAME`
- App grid shows "Mines": `grep -il mines /usr/share/applications/*.desktop` then `dpkg -S` on that file (answer: `gnome-mines`)
- `snap list`
- `grep Commandline /var/log/apt/history.log | tail`
- Task 5: Software & Updates, Updates tab

**Answer key:**

```bash
apt-mark showhold                       # anything held? sudo apt-mark unhold NAME first
sudo apt purge nmap john hydra vsftpd ninvaders gnome-mines SECRET_EXTRA
sudo snap remove --purge nmap
sudo apt autoremove --purge             # read the list, then y
sudo dpkg-reconfigure -plow unattended-upgrades   # answer Yes (or use the Updates tab)
sudo apt update && sudo apt upgrade     # read the summary; y only with 8+ min left
```

**Verify (scorers run these):**

```bash
dpkg -l nmap john hydra vsftpd ninvaders gnome-mines 2>/dev/null | grep -E '^(ii|rc)'   # no output = pass
snap list | grep -E 'nmap|john'         # no output = pass
hash -r; which nmap                     # no output = pass (catches the snap in /snap/bin)
ls /usr/games                           # no ninvaders, gnome-mines, or secret extra
systemctl is-active vsftpd; ss -tln | grep ':21 '   # "inactive" and no listener = pass
which figlet cmatrix                    # both still there = pass (README)
apt-config dump | grep -E 'Periodic::(Update-Package-Lists|Unattended-Upgrade) '   # both "1"
dpkg -l | grep '^rc'                    # no lines from the planted packages
```

Three classic misses: purging apt `nmap` and never checking `snap list`; searching for "mines" when the package is `gnome-mines`; and purging something the README requires. Call all three out. Note that vsftpd started listening on port 21 the moment it was installed - a server package is live as soon as it lands.

## Stretch tasks for second-year students

**Hand this list out at 0:09.** Deliverable: from the logs, work out who installed what and when on your box, and present it in 30 seconds at the exit check. During the challenge, second-years are the scorers and run the verify block on beginners' boxes.

- **History:** `less /var/log/apt/history.log` and `zgrep -h Commandline /var/log/apt/history.log*`. Also `/var/log/dpkg.log`.
- **Holds:** `sudo apt-mark hold openssl`, `apt-mark showhold`, `sudo apt-mark unhold openssl`. A held package silently blocks security updates - a real competition trick.
- **Manual vs automatic:** `apt-mark showmanual | less` - packages someone asked for by name.
- **Where does it come from:** `apt-cache policy nmap` - installed version, candidate version, which repo.
- **Sources:** 22.04 uses `/etc/apt/sources.list`. 24.04 uses `/etc/apt/sources.list.d/ubuntu.sources` (deb822 format). On both, check `/etc/apt/sources.list.d/` for repos that should not be there.
- **Automatic updates:** `apt-config dump | grep Periodic`, `systemctl status unattended-upgrades`.
- **Snap detail:** `snap info firefox`, `snap changes`.

**Optional twist:** before the seat swap, a second-year puts `sudo apt-mark hold` on one planted package on their own box. The next person's purge will complain about held packages until they find it with `apt-mark showhold`.

## What will go wrong

| Problem | Fix |
| --- | --- |
| "Waiting for cache lock ... held by process N (unattended-upgr)" | Normal right after boot. apt waits on its own - let it. Do not kill it or delete lock files. |
| "Permission denied" / "are you root?" | Forgot `sudo`. Up-arrow, or `sudo !!`. |
| Student pressed Enter at the upgrade prompt in Block 2 | Ctrl+C during the download phase is safe. If it is already unpacking, let it finish and pair them with a neighbor. |
| "PLANT FAILED - call the coach" | One package name was not found or the snap failed. Scroll up, read the error, rerun the failed part by hand. |
| Purple "Which services should be restarted?" screen (22.04 mainly) | Tab to Ok, Enter. Accept the defaults. |
| "Configuration file ... modified ... install the package maintainer's version?" | Press Enter (keeps the current file, the default). |
| Packages still upgradable after upgrade ("deferred due to phasing" or "kept back") | Expected. Phased updates roll out gradually. Not a student error. |
| "Unable to locate package" | Typo, or `apt update` was never run on a fresh VM. |
| `dpkg -l` or `less` looks stuck | It is a pager. Press `q`. |
| Software Updater popup appears, or apt says the lock is held by a GUI process | Close the popup or store window, wait a few seconds, rerun. |
| "command not found" for `lolcat` right after install | Run `/usr/games/lolcat` (PATH issue in that shell). |
| `dpkg -S /usr/bin/figlet` says "no path found" | figlet is an alternatives symlink. Use cmatrix for that step. |
| Purge refuses: "Held packages were changed" | `apt-mark showhold`, then `sudo apt-mark unhold NAME`. |
| `cmatrix` will not quit | Press `q` (or Ctrl+C). |
| `which nmap` shows a stale path after removal | `hash -r` or open a new terminal. |

## Exit check (0:56-1:00)

Second-years give their 30-second history report first. Then:

1. What does `apt update` change on your system? (Only the package catalog. No software.)
2. What does `purge` do that `remove` does not? (Deletes the package's system config files.)
3. You purged nmap with apt, but `nmap` still runs. Where else do you look? (`snap list`; also `which nmap`.)
