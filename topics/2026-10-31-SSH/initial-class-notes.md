<!-- markdownlint-disable MD026 -->

# Happy Hallowe'en - It's National More SSH Day!

_The apostrophe honors those who have tricked and treated before us._

## Setup changes for Proxmox

I think you'll like having SPICE display drivers. To review, here's how to do it:

1. Shut down your VM
2. Change the CPU type to `x86-64-v2-AES`
3. Ensure that your display is set to `VirtIO-GPU` (with 256 MB of memory)
4. Download the `virtviewer` software for _your_ computer (not your VM) and install it
5. Profit

> NOTE: After you are using SPICE, you will want to change the scaling factor for Ubuntu desktop to 200%. I can show you how to do this.

## Installing and enabling SSH Server on your VM

SSH Server can run on almost any operating system. To set it up on your Ubuntu Desktop VM, do the following:

```bash
# what does the next command do?
sudo apt update
sudo install openssh-server
```

To enable Ubuntu and make it run at every startup:

## Adding SSH users to your Ubuntu VM

Next, we want to add users that can login to your VM using SSH.

1. In the GUI, go to **Settings → System → Users → Unlock → Add User**
2. Fill in the **Name** field and the **Username** field will autofill
3. Do **not** make the user an Administrator
4. Click Set password now and give the user an easy password. You will probably give this to another student later

## Protecting root

This is a tricky thing to do. For example, if an account that can use `sudo` to become `root` is compromised, an attacker could install an altered version of `sudo` that would log keystrokes to obtain the user's password, and with it, the ability to `sudo` to root.

## Homework

> NOTE: never use AI to solve any of the homework problems UNLESS:
>
> - you have tried yourself and cannot think of what to try next; and
> - you ask the AI to explain it to you.
>
> If you ask the AI for help, tell it:
>
> - your school level ("middle school," or "high school," or your age);
> - how much Linux experience you have; and
> - exactly what you don't understand.
>
> Then ask it to explain its answer step by step. If a step doesn't make sense, say so and ask it to explain that step differently.
>
> If you're still confused after that, you can post your questions on Zulip, or bring them to class.

### Catchup

If you were not present at today's meeting, or if you were present but had computer issues that kept you from staying caught up, the most important thing is to finish all the procedures above so that you're caught up. There is nothing to do for the sections marked as discussions.

### SSH settings

As we saw today, settings for the SSH server software are set in `/etc/ssh/sshd_config/` and `/etc/ssh/sshd_config.d/`. Find a resource online that explains what the most important settins are and what to set them to. Write them down so we can discuss them next week.

### **Extra Credit:** Locking down an SSH user

You can restrict a user so that:

- they can log in to your computer using SSH; but
- they cannot log in to your computer using the Desktop login; and
- they cannot login in to your computer using the Console.

Figure out how, then write up what you did. If you know how to make a GitHub PR, submit your write-up to the cdc-2027 repo under the `docs\ssh-lockdown` folder (make a subfolder is fine).
