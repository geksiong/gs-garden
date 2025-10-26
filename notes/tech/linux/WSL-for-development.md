---
title: WSL2 for development
description: Setting up WSL2 properly for development
date: 2021-11-09
updated: 2022-08-14

tags:
  - wsl2
  - tech

---

## Background

I've been using gitbash for the longest time. I'd briefly tried WSL2 some time back, but I felt more comfortable with gitbash because it is 'outside'. Lately, I've decided to make the move into WSL2 again, for the following reasons:

- VS Code can work with WSL2 from the 'outside'
- Docker Desktop decided to charge for business use. Might as well run docker inside WSL2
- I want to use zsh

## Choose Your Linux

I chose Ubuntu, as most people use it, meaning it should be easier to troubleshoot and seach for solutions.

Debian is much smaller, but it's smaller because a lot of things hasn't been installed yet. Ubuntu comes out of the box with git, ssh, vim, tmux, python, etc, all my usual tools.

## Setup

### File Permissions

This is a common gotcha that non-Linux users probably won't realise. Although WSL2 conveniently auto-mounts your Windows user directory, everything in there has `0777` file permissions, which is horrible for git. Also, I want to keep my ssh configurations outside in Windows, and using ssh is impossible if the keys in my .ssh folder are all `0777`.

Fortunately, WSL2 provides a way to provide proper file permissions for mounted Windows folders. The `.wslconfig` file in Window's `%UserProfile%` directory for some reason didn't work for me. Only `/etc/wsl.conf` inside Ubuntu works.

Create a new file `/etc/wsd.conf` with the following contents:

```ini
[automount]
enabled = true
options = "metadata,umask=22,fmask=11"
```

**Important**: You must wait 8 seconds before launching WSL2 again, before the settings will take effect. You can check whether any WSL2 distributions are running by executing `wsl --list --running` in Windows Command Prompt (or in gitbash)

Not sure why they don't make this the default setting.

I verified it works by changing my ssh key to `0600` and successfully logging into my server.

**References:**

- <https://www.turek.dev/posts/fix-wsl-file-permissions/>
- <https://devblogs.microsoft.com/commandline/chmod-chown-wsl-improvements/>

### VS Code

VS Code (in Windows) can work with the development installed inside WSL2.

Just install Microsoft's "Remote - WSL" extension for VS Code. You can then use it to connect to your WSL2 Linux distribution. You can even simply run `code .` when inside your WSL2 distribution.

### Switching to zsh

```shell
sudo apt install zsh
chsh -s /usr/bin/zsh
```

## Issues

### Fonts

Fonts are lacking in the default command prompt app. Solution: Use Windows Terminal instead.

Tip: Install your favourite color scheme and enable transparency (need to edit settings.json). However, I found that Windows transparency doesn't work with tmux background colors...

### Mounted Windows directories are slow

I originally thought to simply soft-link to my current config files from gitbash, but I soon realise it slows things down quite a bit.

I tested with building the same project, one cloned inside WSL2, and the other cloned under Windows and mounted into WSL2. Building a project from a mounted Windows directory is indeed much slower.

Is this a problem? Let's see...

- For most languages which you can use VS Code for development, keep the projects inside WSL2, since you will need all that tooling you installed in the first place
- Java projects can be developed using VS Code, but most will use IntelliJ or Eclipse. It appears that IntelliJ provides a way to work on your Java projects inside WSL2, and vice versa. Not sure about Eclipse.
- Android projects could be a problem, especially React Native or Ionic projects, where the tooling resides in WSL2, but you need Android Studio to be able to access files in WSL2. **Pending research**
- Building of Docker images may have to compromise (assuming you are running Docker inside WSL2, see below), as they are usually located with the main project, be it outside or inside WSL2. This affects Java projects the most, since they are hosted outside WSL2 (as concluded above). Since this is usually a secondary activity, I think the tradeoff is probably justified. Or you can always run Docker Desktop...

### ~~Running Docker inside WSL2~~

(Update: It just works now!)

~~You can't run Docker out-of-the-box in Ubuntu for WSL2, as WSL2 _does not use systemd_. However, I did find that you can run dockerd and it seems to work.~~

~~Fortunately, there is a very detailed guide on how to setup Docker nicely under WSL2:~~

- <https://dev.to/bowmanjd/you-probably-don-t-need-systemd-on-wsl-windows-subsystem-for-linux-49gn>
- <https://dev.to/bowmanjd/install-docker-on-windows-wsl-without-docker-desktop-34m9>
- <https://dev.to/bowmanjd/using-podman-on-windows-subsystem-for-linux-wsl-58ji>

### ~~Snap packages~~

(Update: probably a none issue now as everyone is moving away from snaps)

~~Ever since Ubuntu introduce Snaps, I find it irritating that some software chose to use snaps for distributions instead of good old apt repositories. Kotlin is one such software...~~

~~Ubuntu under WSL2 does not run snapd, because (you guessed it) systemd. Thanks Ubuntu!~~

## Upgrading Ubuntu

### From Ubuntu 20.04 (Hirsute) to 21.10 (Impish)

Because Ubuntu 20.04 LTS is missing some recent tooling.

- `sudo apt update && sudo apt upgrade`
- `sudo apt remove snapd` -- Needed because WSL doesn't support Systemd directly
- sudo edit `/etc/update-manager/release-upgrades` and change the last line to: `prompt=normal`
- `sudo do-release-upgrade` to upgrade to Hirsute/21.04
- Exit WSL, execute `wsl --terminate Ubuntu` from PowerShell or CMD, and restart WSL/Ubuntu.
- Repeat `sudo do-release upgrade` to upgrade to Impish/21.10

_**Is this safe?**_ So far so good. The current WSL2 kernel is based on 5.10, which is much higher than the 5.4 version released with Ubuntu 20.04, so even though Ubuntu 21.10 is based on 5.13, I don't think it really matters for most WSL2 use cases.

### (Update 2022-08-14) Upgrade to Ubuntu 22.04 (Jammy)

I happened to attempt the upgrade after EOL in July, so I wasn't able to do the usual upgrade method as the Release file has been removed from the Impish repos. To get around this, do the following:

- Reset `/etc/update-manager/release-upgrades` back to `prompt=lts`
- Edit `etc/yum/sources.list` and replace all `xx.archive.ubuntu.com` with `old-releases.ubuntu.com` (official documentation at <https://help.ubuntu.com/community/EOLUpgrades>)
- Then do the usual `sudo apt update` and `sudo do-release-upgrade`

_Note that the WSL2 kernel is still on 5.10, while Jammy is on 5.15 already, but as mentioned it probably doesn't matter._

### (Update 2023-04-11) Systemd support

WSL now supports systemd, so you can run the Docker service without hacks, however you need to get the Microsoft Store version.

- install Microsoft Store version of WSL
- After installation, confirm by running `wsl --version` (this command will fail on the Windows built-in version)
- To get systemd in your distro, need to add this config in `/etc/wsl.conf`:

```ini
[boot]
systemd=true
```

- run `wsl --shutdown` to restart the WSL instance
- inside Ubuntu, run `systemctl list-unit --type=service` and see if docker is there. It should run automatically on boot.
- run `docker ps` etc to confirm the daemon is indeed running and working fine.

### (Update 2023-08-21) VSCode Exec format error

I've been getting `Code.exe: Exec format error` for some time when retrying to launch vscode from the terminal.

According to [this GitHub issue](https://github.com/microsoft/WSL/issues/8952#issuecomment-1568212651) the issue is due to Ubuntu 23.04 for WSL systemd somehow losing the `WSLInterop.conf` file, and the fix is to restore that file:

```sh
sudo sh -c 'echo :WSLInterop:M::MZ::/init:PF > /usr/lib/binfmt.d/WSLInterop.conf'
sudo systemctl restart systemd-binfmt
```

Some users reported they had to unmask and remask `systemd-binfmt.service` (I didn't have to do this):

```sh
sudo sh -c 'echo :WSLInterop:M::MZ::/init:PF > /usr/lib/binfmt.d/WSLInterop.conf'
sudo systemctl unmask systemd-binfmt.service
sudo systemctl restart systemd-binfmt
sudo systemctl mask systemd-binfmt.service
```

### Updating WSL2 kernel

Just run `wsl --update` inside a PowerShell terminal.

## Software to Install

This is my personal list:

### Development

- nodejs - Just add their apt repo
- java / kotlin - Install zip & unzip, then install sdkman. Note that you need to use sdkman to manage your installed java tooling.
- podman - requires Ubuntu >= 20.10

### Terminal Quality of Life

- fzf
- bat - in Ubuntu/Debian, the command is batcat due to conflicts with another package
- exa - requires Ubuntu >= 20.10
- pfetch - need to install manually
