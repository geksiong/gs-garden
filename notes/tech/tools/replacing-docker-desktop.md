---
title: Replacing Docker Desktop
description: Seeking alternatives to running Docker Desktop
date: 2023-11-30

tags:
  - containers
  - docker
  - rancher

---

## Why remove Docker Desktop?

Since I may do work on both work and home machines, I want to avoid using Docker Desktop on my home machine so that I don't run afoul of their licensing. I've already removed Docker Desktop from my work laptop ever since they changed their licensing policies.

## Linux: nothing to do

Frankly, I haven't found a reason to have Docker Desktop on Linux in the first place. Container technology is Linux-native, and Docker Core which is open source is exactly what I use on servers.

## Windows: use WSL

Replacing or avoiding Docker Desktop on Windows is easily done thanks to WSL2. It's basically Linux.

Read my [notes on setting up WSL2](WSL-for-development.md]

## MacOS: Rancher?

MacOS does not have an equivalent to WSL.

I've been trying out Rancher Desktop. Unlike Podman which is a replacement for Docker core, Rancher apparently runs Docker inside a VM. An additional benefit is that it also does Kubernetes.

### Rancher issue: File permissions with bind mounts (RESOLVED?)

I prefer to use designated bind mounts for the data volumes, and here I ran into a problem with file permissions. In Linux (and WSL), the bind mounts must have the same UID:GID as the process running the container. This might require some juggling of the users on the host, but it is easily understood and it works.

On MacOS, Rancher doesn't seem to do this. Trying to run the official MySQL container keep spewing out file permissions errors such as `chown: changing ownership of '/var/lib/mysql/': Permission denied`. I found a match of this problem at https://github.com/rancher-sandbox/rancher-desktop/issues/1209#issuecomment-1370181132.

The suggested workaround is to include the following content in ~/Library/Application\ Support/rancher-desktop/lima/_config/override.yaml:

```yaml
mountType: 9p
mounts:
  - location: "~"
    9p:
      securityModel: mapped-xattr
      cache: "mmap"
```

The caveat is that any symlinks on your host system will be seen as the referenced object in the VM/container.

Alas for me, **MySQL does use symlinks**. The file permission errors are mostly resolved except for the symlink used by MySQL for the socket file...

Interestingly, the file permission on the host must be owned by me, not the container's user. I suppose this is to maintain the same (quirky) behaviour as Docker Desktop for Mac?

Following another suggestion from the same issue page, I tried switching to the **experimental** virtiofs volume mount type with VZ emulation, and that seems to work. I guess that's what I'll stick to for now. Hope this is stable and comes of experimental status soon.
