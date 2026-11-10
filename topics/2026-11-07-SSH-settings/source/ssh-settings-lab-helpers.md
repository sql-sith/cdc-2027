# SSH Settings Lab: Helper Cheat Sheet

For parents and helpers. The student handout is [ssh-settings-lab.md](ssh-settings-lab.md).

## Common problems

| Problem | Fix |
| --- | --- |
| `sshd -t` says `Missing privilege separation directory: /run/sshd` | SSH isn't running. That folder only exists while it runs. Run `sudo systemctl start ssh`, then try again. This happens on a freshly booted VM that nobody has connected to yet, and after a restart that failed. |
| `sshd -t` prints an error with a line number | A typo in `05-lab.conf`. The error names the file and line. |
| A change "didn't work" | Did they restart SSH? Run `sudo sshd -T \| grep -i <setting>` to check what the file says. |
| Tester can't connect at all | Check `ping <IP>`, `systemctl status ssh` and `sudo ufw status` on the owner's VM. |
| Homework user is refused in Exercise 3 step 1, before `AllowGroups` is turned on | The owner probably put `AllowGroups` in the main `/etc/ssh/sshd_config` for the extra-credit homework. Check with `sudo sshd -T \| grep -iE 'allowgroups\|allowusers'` and comment out that line in the main file. |
| `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED` | The partner's VM was rebuilt. The tester runs `ssh-keygen -R <IP>`. |
| Nothing from SSH shows in the log | `LogLevel` is still `QUIET`, or the change wasn't applied. |
| `ssh-keygen` says a key already exists | Answer `n`. `ssh-copy-id` will use the existing key. |
| Everything is a mess | Retype `05-lab.conf` from the setup step and restart SSH. |

## Answers

### Exercise 1

- With `LogLevel QUIET`, SSH itself logs nothing. What still appears comes from PAM, the library that checks passwords: `pam_unix(sshd:auth): authentication failure; ... rhost=<IP> user=labuser` for the wrong password, and `pam_unix(sshd:session): session opened for user labuser ...` / `session closed ...` for the good login. The lesson: silencing one program's logging doesn't silence everything, but the PAM lines say much less (no port, no login method, and nothing at all for key logins).
- With `LogLevel VERBOSE`, the SSH lines are `Accepted keyboard-interactive/pam for labuser from <IP> port <N> ssh2` and `Failed keyboard-interactive/pam for labuser from <IP> port <N> ssh2`, plus connection and disconnection lines. They say `keyboard-interactive`, not `password`, because the SSH client tries keyboard-interactive before password when a server offers both, and the lab file offers both. The wording becomes `Accepted password` / `Failed password` only after `KbdInteractiveAuthentication` is turned off in Exercise 5.
- Noticing `QUIET`: check settings with `sshd -T`, compare against a saved baseline, or send logs to another machine.

### Exercise 2

- Root's password is locked on Ubuntu (`*` in `/etc/shadow`, shown as `L` by `passwd -S`), so no password can match, even with `PermitRootLogin yes`. The tester is prompted and gets "Permission denied, please try again."
- The log shows `Failed keyboard-interactive/pam for root from <IP> ...` and `pam_unix(sshd:auth): authentication failure ... user=root`.
- Setting `PermitRootLogin no` anyway is defense in depth: if someone later gives root a password (`sudo passwd root`), SSH still refuses root.
- Don't expect the log to change after `PermitRootLogin no`. SSH checks that setting only *after* a password or key has been accepted, and root's never is, so the attempts look identical. A `ROOT LOGIN REFUSED` line would only appear if root had a password and the tester guessed it.
- From the outside, the tester can't tell which lock stopped them. SSH asks for a password either way.

### Exercise 3

- The log line says the user is `not allowed because none of user's groups are listed in AllowGroups`.
- No restart is needed after adding the user to the group. SSH checks group membership at every login; only changes to the settings file need a restart.

### Exercise 4

- The key's passphrase unlocks the key on the tester's own VM. It never goes over the network. `labuser`'s password would be checked by the partner's server.
- On Ubuntu Desktop, GNOME's key agent may show the passphrase question as a pop-up window. If the student ticks "remember," later logins won't ask again. That's fine for the lab.

### Exercise 5

| `PasswordAuthentication` | `KbdInteractiveAuthentication` | Password login works? | "can continue" list |
| --- | --- | --- | --- |
| yes | yes | yes | `publickey,password,keyboard-interactive` |
| no | yes | **yes (the trap)** | `publickey,keyboard-interactive` |
| yes | no | yes | `publickey,password` |
| no | no | no | `publickey` |

- Two prompt wordings appear. Keyboard-interactive looks like `(labuser@<IP>) Password:`; that's what the students have seen all lab long, and it stays the same in the `no / yes` row. The plain password prompt, `labuser@<IP>'s password:`, appears only in the `yes / no` row. In the `no / no` row there is no prompt at all, just `Permission denied (publickey)`.
- The habit wrap-up question 2 is after: test the result from the outside, and check `sshd -T`, instead of trusting that a change worked.

## Before class

Run through the lab once on a real Ubuntu 24.04 VM. The log wording and the Exercise 5 results above were worked out from OpenSSH's documentation and source, not from a test run.
