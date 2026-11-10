# Cyber Defense Club

Nov 7, 2026 ∙ 10:00 AM to 12:30 PM ∙ Chris Leonard

AI-generated content in notes may be incorrect. Learn more
Remember, the notes will often say Chris is talking when someone else is. That is because the audio for everyone in the conference room is recorded through Chris's mic.

## Decisions

- Complete the SSH lab independently and bring questions next week
- Validate SSH configuration before every restart

## Meeting notes

### System Access

- Participants encountered difficulty logging in and reported that the display was not showing as expected
- Chris noted that a release update was available and discussed updating the computer
- Chris discussed using standard VGA and the second SPICE option as alternatives for display access, noting that Wayland was sensitive to display changes
- Chris reported that the attempted local privilege-escalation methods did not work on the computer

### SSH Security

- Chris discussed configuring SSH to prefer key authentication over password authentication and noted that leaving keyboard-interactive authentication enabled might still allow password prompts even when password authentication was disabled
- Chris said the lab configuration enabled both password and keyboard-interactive authentication and allowed root login intentionally, and noted that these settings were deliberately insecure for the lab
- Chris noted that running SSH on a non-root user while retaining port 22 would require a port proxy
- Chris described an exercise in which participants changed a logging setting, had someone log in over SSH, and compared the resulting logs; he noted that PAM Unix logins did not count as SSH logins
- Chris said the SSH discussion had reached its time limit and that participants would continue independently and bring questions the following week

### SSH Configuration

- Chris explained that files in the SSHD configuration directory were processed in alphabetical order before the main SSHD configuration file, and that the first setting encountered would take precedence
- Chris noted that an SSH configuration file in the configuration directory could override settings such as password authentication and keyboard-interactive authentication, so the directory contents needed to be checked
- Chris explained that SSH configuration snippets were processed alphabetically, so a file prefixed with `10` would be processed before one prefixed with `50`; the lab used a `05` configuration file to ensure no smaller-numbered file took precedence
- Chris described the lab workflow as editing the SSH configuration, running a validation script, and restarting SSH only when the configuration was valid
- Chris pointed out that the `ssh -T` option provided another SSH configuration-testing command
- Chris explained that the tool could dump all currently configured settings or display a specific setting, including the full contents of multi-line settings
- Chris explained that the SSHD service was inactive and disabled, and demonstrated starting it immediately and enabling it to start automatically when the virtual machine restarted
- Chris noted that the configuration-directory check found no files to rename, so the step for renaming existing files did not apply in this environment
- Chris explained that the SSH configuration change had to be written and applied after every edit for the change to take effect

### User and Group Management

- Chris cautioned that adding an existing user to a group without the `-a -G` parameters would remove the user from all existing groups while adding the new group
- Chris explained that the lab user setup accepted a weak password with a warning because a PAM policy would be required to reject it, and that the remaining profile fields were optional

### Shell Command Execution

- Chris explained that `sshd -t &&` could validate the SSH configuration before restarting SSH, because the restart would not run if validation returned a failure; he also used `apt update && apt upgrade --yes` to illustrate the same short-circuit behavior
- Chris explained that a zero exit code indicated successful command execution, while a non-zero exit code indicated failure, and that scripts could use the exit code to decide whether to continue
- Chris demonstrated that `&&` allowed a subsequent command to run only when the preceding command succeeded

### Access Control

- Chris discussed using cron to schedule jobs and emphasized applying least privilege by avoiding unnecessary root execution for services and scheduled tasks

### Lab Environment Setup

- Chris explained that the lab repository could be cloned either inside the virtual machine and accessed through a browser there, or on the local computer with content pasted into the VM through SPICE; he said using a local browser would be faster if SPICE paste worked
- Chris advised using the copy icon in the GitHub interface when pasting commands into the terminal because copying highlighted web-page text could introduce extra characters
- Chris said the lab could be practiced on one virtual machine using its real IP address, or with a second virtual machine acting as the SSH client

### Linux File Editing

- Chris explained that `sudo edit` copies the target file to a temporary user-writable location, lets the editor run without root privileges, and writes the file back when editing ends; he described it as safer because a crash would not leave a swap file or break the original file
- Chris noted that Linux text files should end with a newline and said `sudo edit` handles placing the edited file correctly

### SSH Monitoring

- Chris recommended checking `systemctl status ssh` after starting SSH and using the journal to follow SSH log messages as they appear
- Chris said the next lab step was to add SSH messages by changing the SSH log level to `verbose`, writing the configuration change, and restarting the service
- Chris demonstrated searching the configuration for the log-level setting and changing it to `verbose` so SSH login activity would be recorded in more detail
- Chris said that journal entries showing an SSH systemd service restart could help identify unexpected changes, since the service should not normally be restarted

### SSH Testing

- The lab login test used the `lab user` account and required exiting the session afterward; entering an incorrect password produced repeated failed login attempts visible in the journal before the attempt was interrupted
- Chris described the planned test as checking the lab user with both a correct and an incorrect password after the configuration change, noting that the service must be handled after each change
- Chris explained that the test should include both successful and failed logins, with the resulting messages reviewed in the journal

### Authentication

- Chris explained that the login messages shown were generated by PAM Unix and PAM rather than SSH. He described PAM as the Linux framework that supports pluggable authentication methods, including password, Google authentication, and biometric plugins
- Chris noted that SSH and PAM log messages could reveal different parts of an authentication attempt, including PAM authentication information brokered through SSH

### Configuration Integrity

- Participants discussed detecting unauthorized changes to SSH or PAM logging by monitoring configuration-file changes and using a drift monitor
- Chris discussed protecting configuration files by removing write permissions or having a configuration-management system restore the expected contents; they also discussed using a read-only mounted image or a recurring job as temporary safeguards
