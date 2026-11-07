# SSH Settings Lab: Helper Cheat Sheet

For parents and helpers. The student handout is [ssh-settings-lab.md](ssh-settings-lab.md).

## Common problems

| Problem | Fix |
|---|---|
| `sshd -t` says `Missing privilege separation directory: /run/sshd` | Run `sudo mkdir -p /run/sshd`. This is a quirk of Ubuntu 24.04 when SSH hasn't started yet. |
| `sshd -t` prints an error with a line number | A typo in `05-lab.conf`. The error names the file and line. |
| A change "didn't work" | Did they restart SSH? Run `sudo sshd -T \| grep -i <setting>` to check what the file says. |
| Tester can't connect at all | Check `ping <IP>`, `systemctl status ssh` and `sudo ufw status` on the owner's VM. |
| `WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED` | The partner's VM was rebuilt. The tester runs `ssh-keygen -R <IP>`. |
| Nothing shows in the log | `LogLevel` is still `QUIET`, or the change wasn't applied. |
| Everything is a mess | Retype `05-lab.conf` from the setup step and restart SSH. |

## Answers

### Exercise 1

- With `LogLevel QUIET`, nothing appears in the log, not even the failed password.
- With `LogLevel VERBOSE`, the log shows lines like `Accepted password for labuser from <IP>` and `Failed password for labuser from <IP>` (or `Failed keyboard-interactive/pam` while keyboard-interactive is on).
- Noticing `QUIET`: check settings with `sshd -T`, compare against a saved baseline, or send logs to another machine.

### Exercise 2

- The log line says the user is `not allowed because none of user's groups are listed in AllowGroups`.
- No restart is needed after adding the user to the group. SSH checks group membership at every login; only changes to the settings file need a restart.

### Exercise 3

- The key's passphrase unlocks the key on the tester's own VM. It never goes over the network. `labuser`'s password would be checked by the partner's server.

### Exercise 4

| `PasswordAuthentication` | `KbdInteractiveAuthentication` | Password login works? | "can continue" list |
|---|---|---|---|
| yes | yes | yes | `publickey,password,keyboard-interactive` |
| no | yes | **yes (the trap)** | `publickey,keyboard-interactive` |
| yes | no | yes | `publickey,password` |
| no | no | no | `publickey` |

- The prompts differ slightly. Keyboard-interactive looks like `(labuser@<IP>) Password:`. A plain password prompt looks like `labuser@<IP>'s password:`.
- The habit wrap-up question 2 is after: test the result from the outside, and check `sshd -T`, instead of trusting that a change worked.

### Exercise 5

- Root's password is locked on Ubuntu (`L` in `passwd -S`), so no password can work even with `PermitRootLogin yes`.
- Setting `PermitRootLogin no` anyway is defense in depth: if someone later gives root a password, SSH still refuses root.
- From the outside, the tester can't tell which lock stopped them. SSH asks for a password either way.

## Before class

Run through the lab once on a real Ubuntu 24.04 VM. The Exercise 4 results and the exact log wording above haven't been checked on one yet.
