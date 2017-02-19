# image-builder-rpi
[![Build Status](https://travis-ci.org/StefanScherer/image-builder-rpi.svg?branch=cloud-init)](https://travis-ci.org/StefanScherer/image-builder-rpi)

**Notice**: This is a fork of hypriot/image-builder-rpi to test cloud-init. See the GitHub releases for special SD card images.

## Flash

Flash one of the released SD card images

```bash
flash https://github.com/StefanScherer/image-builder-rpi/releases/download/v1.3.1/hypriotos-rpi-v1.3.1.img.zip
```

After flashing add two files to the FAT partition.

### /boot/user-data

```
#cloud-config
hostname: black-pearl
# fqdn: mynode.example.com
manage_etc_hosts: true
users:
  - name: pirate
    primary-group: users
    sudo: ALL=(ALL) NOPASSWD:ALL
    groups: users,docker,video
    ssh-import-id: None
    lock_passwd: true
    ssh-authorized-keys:
      - ssh-rsa AAAAB3NzaC...RmWNN user@client
```

### /boot/meta-data

```
hostname: black-pearl
```

## Run integration tests

Now flash the SD card image and boot up a Raspberry Pi. Run the [Serverspec](http://serverspec.org) integration tests in `builder/test-integration/`
folder against your Raspberry Pi. Set the environment variable `BOARD` to the
IP address or host name of your running Raspberry Pi.

```bash
flash hypriotos-rpi-dirty.img.zip
BOARD=black-pearl.local make test-integration
```

## License

MIT - see the [LICENSE](./LICENSE) file for details.
