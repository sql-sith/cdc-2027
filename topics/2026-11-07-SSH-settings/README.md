# SSH Settings

Which `sshd_config` settings matter most, and a hands-on lab where you and a lab partner change them on each other's VMs and watch what happens.

## What we covered

- **The settings, ranked.** Fourteen `sshd_config` settings in three tiers (must cover, should cover, worth a mention), each with what it does and why it matters. The top three: `PasswordAuthentication`, `PermitRootLogin`, and `AllowGroups`.
- **How the config files combine.** Files in `/etc/ssh/sshd_config.d/` are read before `sshd_config`, in alphabetical order, and the first value for a setting wins.
- **The lab.** Two people, two VMs. On your own VM you're the *owner* who changes settings and reads the logs; on your partner's VM you're the *tester* who tries to log in. You start from a settings file that is insecure on purpose and fix it one exercise at a time: logging, root, who is allowed in, keys, and the password trap, where turning off `PasswordAuthentication` alone doesn't stop passwords.
- **Two commands to keep.** `sudo sshd -t` checks the config for errors before you restart SSH; `sudo sshd -T` shows every setting SSH is actually using.

## Before the lab

You need a working SSH server and a user you can share with your partner. That was the [Oct 31 homework](../2026-10-31-SSH/).

## Files

| File | What it is |
|---|---|
| [ssh-settings.pdf](ssh-settings.pdf) | The ranked settings tables |
| [ssh-settings-lab.pdf](ssh-settings-lab.pdf) | The student lab handout: setup, five exercises, stretch work, wrap-up questions, cleanup |
| [ssh-settings-lab-helpers.pdf](ssh-settings-lab-helpers.pdf) | For parents and helpers: a troubleshooting table and the answers. Do the lab before you read it |
| [source/](source/) | Markdown originals of all three documents |

If a step in the lab doesn't behave the way the handout says, tell Chris. The handout was written from how OpenSSH is documented to work, and the helper sheet lists what hadn't been tried on a real VM yet.
