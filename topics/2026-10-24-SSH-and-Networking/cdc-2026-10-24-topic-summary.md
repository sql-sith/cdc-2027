**Oct 24 session - what we covered**
(full write-up in the session guide; section numbers match)

| #  | Topic                              | Summary                                                                                                                                                                   |
| -- | ---------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1  | VirtIO GPU display                 | Switch Display from Spice to VirtIO GPU; takes effect after VM restart; viewed with Virt Viewer                                                                           |
| 2  | ip command basics                  | `ip address`; 127.0.0.1/8 loopback; /24 = first three octets are the network, last is the host                                                                          |
| 3  | The /24 -> /0 mistake              | How the GUI ended up with /0, why the host then thinks nothing is local, ping test with 0 replies                                                                         |
| 4  | Correct network config             | Your VM address, which is 144.76.90.<VM ID>, mask 255.255.255.0, gateway 144.76.90.254, DNS 199.100.16.100; verify with ping                                             |
| 5  | wget and missing CSS               | `wget` the Google page, open via `file://` in Firefox, why it's unstyled                                                                                              |
| 6  | What SSH is                        | Bare `ssh` shows the client is preinstalled; secure shell from one computer to another                                                                                  |
| 7  | Enable the service                 | `systemctl status ssh`, `enable`, `start`, re-check: enabled + active                                                                                               |
| 8  | Install on a second VM             | `apt update` first (and why), then `apt install openssh-server`, enable and start                                                                                     |
| 9  | First connection + host key        | Fingerprint prompt, saved to `~/.ssh/known_hosts`, changed hash later = warning                                                                                         |
| 10 | Port 22 lookup                     | `grep 22 /etc/services` shows ssh = remote login                                                                                                                        |
| 11 | Add a user                         | Creating accounts in Settings > Users                                                                                                                                     |
| 12 | Connecting as a user               | `ssh username@<server IP>`; `hostname` and `ip address` to confirm which VM you're on                                                                               |
| 13 | Users, passwd, shadow, permissions | Default home directory, account isolation, `/etc/passwd` fields and the x placeholder, `/etc/shadow` hashes and `!` service accounts, `ls -l` permission triplets |
| 14 | Next week: sshd_config             | SSH vs sshd (daemon), sshd_config.d, why moving off port 22 doesn't hide the service; coming up: ListenAddress, host keys, authorized_keys, TOTP                          |
