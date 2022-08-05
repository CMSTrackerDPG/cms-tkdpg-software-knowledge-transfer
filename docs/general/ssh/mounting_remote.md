# Mounting remote locations via SSH

[Source](https://linuxize.com/post/how-to-use-sshfs-to-mount-remote-directories-over-ssh/)[^1]
[^1]: <small>Thx 哈拉</small>


## Installation

- `sudo apt install sshfs`

## Mounting

- Make a mountpoint for the remote filesystem:
  ```bash
  mkdir ~/lxplus
  ```
- Mount it:
  ```bash
  sshfs <username>@lxplus.cern.ch:/afs/cern.ch/user/<username's first letter>/<username/ ~/lxplus
  ```
