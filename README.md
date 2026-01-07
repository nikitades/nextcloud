# Nextcloud AIO Ansible Provisioning

## Overview

This repository contains Ansible playbooks and roles to provision a server for Nextcloud AIO using Docker Compose.

## Quick Start

1. Edit `ansible/inventory.ini` and set your host.
2. Provide required secrets when running the playbook using `--extra-vars`.

Example command that passes all required variables:

```bash
#!/bin/bash

ansible-playbook -i ansible/inventory.ini ansible/playbook.yml \
    --extra-vars "smb_server=u123456.your-storagebox.de" \
    --extra-vars "smb_share=backup" \
    --extra-vars "smb_password='somepwd'" \
    --extra-vars="smb_username=u123456" \
    -vvv
```

## Backups and Recovery

### Initial Backup

To back up a server for the first time, use the AIO admin interface on port 8080.

**Important:** Save the backup password. It will be impossible to restore backups later if this password is lost.

To retrieve the password, explore the nextcloud-aio-mastercontainer volume:

```bash
docker run -it --rm -v nextcloud_aio_mastercontainer:/mnt/data alpine /bin/sh
```

Then run `cat /mnt/data/data/configuration.json` and locate the `BORG_BACKUP` password in the list of secrets. The admin secret is also stored here.

### Restore Backup

To restore a backup, you have only one chance after the initial setup has been applied.

Do not specify any domains or configuration. Attach the backups volume and verify via SSH that the files are available. Then specify the mount path (for example `/mnt/HC_Cloud_1234566/backup`) and the password saved during the initial backup. The restore will proceed automatically.

**Warning:** All passwords, including the admin passphrase and all user passwords, will be overwritten during restore.

## Recovery

Keep `ansible/group_vars/all.yml` and the `.env` secrets safe. To recreate the server: install OS, add SSH key, run playbook.