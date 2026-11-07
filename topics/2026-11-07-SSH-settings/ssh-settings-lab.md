# SSH Settings Lab (90 minutes)

**Roles:** On **your** VM, you are the **owner**: you change settings and watch the logs. On your **partner's** VM, you are the **tester**: you try to log in and report what happened. Every exercise, you do both at once.

| Time | Part |
|---|---|
| 0:00 | Setup |
| 0:15 | Exercise 1: Logging |
| 0:25 | Exercise 2: Who is allowed in |
| 0:40 | Exercise 3: Keys |
| 0:55 | Exercise 4: The password trap |
| 1:15 | Exercise 5: Root |
| 1:20 | Wrap-up and cleanup |

## Setup (owner)

1. Check what's in the settings folder:

   ```bash
   ls /etc/ssh/sshd_config.d/
   ```

   If any `.conf` files are listed, turn each one off by renaming it. For example:

   ```bash
   sudo mv /etc/ssh/sshd_config.d/10-allowgroups.conf /etc/ssh/sshd_config.d/10-allowgroups.conf.off
   ```

   Only files ending in `.conf` are read.

2. Create the lab group and lab user. Give `labuser` a simple password and tell your partner what it is.

   ```bash
   sudo groupadd sshlab
   ```

   ```bash
   sudo adduser labuser
   ```

   ```bash
   sudo usermod -aG sshlab labuser
   ```

   You also need the SSH user you made for homework. If you don't have one, make it now with `sudo adduser`, but **don't** add it to `sshlab`.

3. Create the lab settings file. The name starts with `05` so SSH reads it first, which means its settings win.

   ```bash
   sudoedit /etc/ssh/sshd_config.d/05-lab.conf
   ```

   Type this in exactly, then save:

   ```text
   LogLevel QUIET
   #AllowGroups sshlab
   PasswordAuthentication yes
   KbdInteractiveAuthentication yes
   PermitRootLogin yes
   ```

   This file is **insecure on purpose**. You'll fix it during the lab.

4. Apply it, then give your partner your IP address:

   ```bash
   sudo sshd -t && sudo systemctl restart ssh
   ```

   ```bash
   ip -br a
   ```

### The change routine

You'll do this after every change:

1. Edit: `sudoedit /etc/ssh/sshd_config.d/05-lab.conf`
2. Check and apply: `sudo sshd -t && sudo systemctl restart ssh`
3. Confirm: `sudo sshd -T | grep -i <setting name>`

> **NOTE:** `sshd -T` reads the **file**, not the running server. If you skip step 2, `-T` shows your new value but SSH still behaves the old way.

## Exercise 1: Logging (`LogLevel`)

1. **Owner:** open a second terminal and watch the SSH log:

   ```bash
   sudo journalctl -u ssh -f
   ```

2. **Tester:** log in as `labuser` with the password, then type `exit`. Then try again and type the password **wrong** on purpose.
3. **Owner:** what showed up in the log?
4. **Owner:** change `LogLevel QUIET` to `LogLevel VERBOSE` and apply it. **Tester:** repeat step 2.
5. **Owner:** find the lines for the successful login and the failed one. What do they tell you?

**Think about it:** an attacker who gets root might set `LogLevel QUIET`. How would you notice?

## Exercise 2: Who is allowed in (`AllowGroups`)

1. **Tester:** log in as your partner's **homework user** with its password. It should work.
2. **Owner:** remove the `#` in front of `AllowGroups sshlab` and apply it.
3. **Tester:** try the homework user again, then try `labuser`.
4. **Owner:** find the log line that explains why the homework user was refused.
5. **Owner:** add the homework user to the group. **Tester:** try again. Did the owner need to restart SSH? Why or why not?

   ```bash
   sudo usermod -aG sshlab <homework user>
   ```

6. **Owner:** take the user back out of the group:

   ```bash
   sudo gpasswd -d <homework user> sshlab
   ```

**Notice:** the tester only sees "Permission denied." The owner's log says *why*. Attackers get less information than defenders do.

## Exercise 3: Keys

1. **Tester:** make a key on your own VM. Give it a passphrase.

   ```bash
   ssh-keygen -t ed25519
   ```

2. **Tester:** copy it to your partner's `labuser`. This asks for `labuser`'s password one last time.

   ```bash
   ssh-copy-id labuser@<partner IP>
   ```

3. **Tester:** log in as `labuser`. You might be asked for your **key's passphrase**, but not `labuser`'s password. What's the difference?
4. **Owner:** in the log, find the `Accepted publickey` line and read out the `SHA256:...` value. **Tester:** compare it with your key's fingerprint:

   ```bash
   ssh-keygen -lf ~/.ssh/id_ed25519.pub
   ```

## Exercise 4: The password trap

Goal: stop **all** password logins, while keys keep working.

1. **Owner:** set `PasswordAuthentication no` and apply it.
2. **Tester:** try a password login. The `-o` option tells your SSH program not to use your key, so you're testing passwords only.

   ```bash
   ssh -o PubkeyAuthentication=no labuser@<partner IP>
   ```

   Did you get in? Look closely at the password prompt. Is it worded the same as before?

3. **Tester:** run it again with `-v` added, and press **Ctrl+C** at the password prompt. Scroll up and find the line `Authentications that can continue:`. What does it list?
4. **Owner:** figure out which setting is still letting passwords in, change it, and apply it. **Tester:** test again.
5. Together, try all four combinations and fill in this table:

   | `PasswordAuthentication` | `KbdInteractiveAuthentication` | Password login works? | "can continue" list |
   |---|---|---|---|
   | yes | yes | | |
   | no | yes | | |
   | yes | no | | |
   | no | no | | |

6. Finish with both set to `no`. **Tester:** confirm your key login still works.

## Exercise 5: Root (`PermitRootLogin`)

1. **Tester:** try `ssh root@<partner IP>` and guess some passwords. The setting says `yes`, so why can't you get in?
2. **Owner:** run this. What does the `L` mean?

   ```bash
   sudo passwd -S root
   ```

3. **Owner:** set `PermitRootLogin no` anyway and apply it. Why bother, if root is already locked?

## Stretch (if you finish early)

- **`MaxAuthTries`:** temporarily set `PasswordAuthentication yes` and `MaxAuthTries 2`. **Tester:** type wrong passwords. What happens, and what does the log say? Set both back afterward.
- **`Banner`:** put a warning in `/etc/issue.net` with `sudoedit`, then add `Banner /etc/issue.net`. **Tester:** log in and read it.
- **`sshd -T -C`:** ask SSH what would happen for a specific user without logging in:

  ```bash
  sudo sshd -T -C user=labuser,host=x,addr=10.0.0.5 | grep -i allowgroups
  ```

## Wrap-up questions

1. Which setting surprised you most?
2. You set `PasswordAuthentication no` and it didn't fully work. What habit catches mistakes like that?
3. Exercise 2 gave the tester much less information than the owner. Why is that good for defenders?

## Cleanup (or keep it for next week)

1. Delete the lab settings file:

   ```bash
   sudo rm /etc/ssh/sshd_config.d/05-lab.conf
   ```

2. Rename any `.off` files back to `.conf`.
3. Remove the lab user and group:

   ```bash
   sudo deluser --remove-home labuser
   ```

   ```bash
   sudo delgroup sshlab
   ```

4. Apply the change:

   ```bash
   sudo systemctl restart ssh
   ```
