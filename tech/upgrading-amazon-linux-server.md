---
title: Upgrading Amazon Linux server
description: Migrating an EC2 instance from Amazon Linux 1 to Amazon Linux 2023
date: 2023-12-16

tags:
- amazon linux
- aws

---
Unlike Ubuntu, Amazon Linux does not have an in-place upgrade capability, so with the EOL of Amazon Linux 1 at end of 2023, I was recently forced to migrate my servers to the latest Amazon Linux 2023. These are just some notes on my process:

## Move the apps into containers for easier management.

Containerising the apps will make them less dependent on the host and easier to manage.

Most needs can be achieved with the official containers and Docker Compose. I didn't fully containerise though, due to lack of devops support, and I wanted to minimise the extent of change.

- Store the data on the host, and mount the data folders (config, data, logs) into the containers.
- Consolidate all data to `/opt` directory, which will be a separate disk mounted to the host.
- For MySQL, you can run external SQL scripts with `docker exec -i <container_id> mysql <options> < <sql_file>` (note: the typical `-t` flag must be omitted)
- Docker on Linux will write to the mounted volumes as the container user. It is better to change the host user/group owner to match the container, than to try to change the user inside the container as doing the latter may break the app depending on how the container was built.
- Some things to remember to do for `docker-compose.yaml`:
  - Set the timezone
  - Use 'local' logging driver

## Switching OS

Assuming everything has been running fine with Docker compose, we can schedule a time to switch the OS.

- Start a new server with the latest Amazon Linux. Just use the micro ones, we won't be needing them.
- Remember to use the sake key as your original server.
- Once you can ssh in:
  - do the usual `yum update`
  - set timezone: `sudo timedatectl set-timezone <your_time_zone>`
  - update hostname in `/etc/hostname`
  - install Docker: `sudo yum install docker`
  - Docker compose must be installed manually:
    - ```
      sudo mkdir -p /usr/local/lib/docker/cli-plugins
      sudo curl -SL https://github.com/docker/compose/releases/latest/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/lib/docker/cli-plugins/docker-compose
      sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
      ```
  - if need crontab: `sudo yum install cronie cronie-anacron`
  - if need CloudWatch: `sudo yum install amazon-cloudwatch-agent`
  - if need swap file create it
- Reboot and check eveyrthing is still ok
- Shutdown, detach the old boot on the old server, and attach the new boot disk. This way ensures all your existing IP addresses, security routes, etc will remain intact.
- The `/opt` disk is still attached, but it is not known to the new OS.
- Startup and ssh in. Mount the `/opt` disk and update `/etc/fstab`
- Reboot and ssh in again to verify the `/opt` disk is mounted on boot.
- Go into your `/opt` folder (which is a separate disk), wherever your docker compose stuff is, and just do a `sudo docker compose up -d`.
- Check that everything runs ok. Reboot again just to be sure.
