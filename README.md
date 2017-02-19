# image-builder-rpi
[![Build Status](https://travis-ci.org/StefanScherer/image-builder-rpi.svg?branch=cloud-init)](https://travis-ci.org/StefanScherer/image-builder-rpi)

**Notice**: This is a fork of hypriot/image-builder-rpi to test cloud-init. See the GitHub releases for special SD card images.

## Flash

Here is an example how all the GitHub repos play together:

![Architecture](http://blog.hypriot.com/images/hypriotos-xxx/hypriotos_buildpipeline.jpg)


Before you flash, prepare your cloud-init config files.
Flash one of the released SD card images

```bash
flash https://github.com/StefanScherer/image-builder-rpi/releases/download/v1.3.1/hypriotos-rpi-v1.3.1.img.zip
```

After flashing add two files to the FAT partition.

### /boot/user-data

The file `user-data` can configure the hostname, add users, SSH keys etc. See [Cloud config examples](http://cloudinit.readthedocs.io/en/latest/topics/examples.html) for some more use cases.

```
#cloud-config
hostname: black-pearl
# fqdn: mynode.example.com
manage_etc_hosts: true
runcmd:
 - [ systemctl, restart, avahi-daemon ]
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

### Run flash script

Now get the latest version of the [flash script](https://github.com/hypriot/flash) to support the new cloud-init options.

Flash one of the released SD card images with

```bash
flash -u ./user-data -m ./meta-data <URL to hypriotos-rpi-v*.img.zip>
```

## Contributing

You can contribute to this repo by forking it and sending us pull requests.
Feedback is always welcome!

You can build the SD card image locally with Vagrant.

### Setting up build environment

Make sure you have [vagrant](https://docs.vagrantup.com/v2/installation/) and [docker-machine](https://docs.docker.com/machine/install-machine/) installed.
Then run the following command to create the Vagrant box and the Docker Machine
connection. The Vagrant box is needed as a vanilla boot2docker VM is not able to
run guestfish inside. Use `export VAGRANT_DEFAULT_PROVIDER=virtualbox` to
strictly create a VirtualBox VM.

## Run integration tests

Now flash the SD card image and boot up a Raspberry Pi. Run the [Serverspec](http://serverspec.org) integration tests in `builder/test-integration/`
folder against your Raspberry Pi. Set the environment variable `BOARD` to the
IP address or host name of your running Raspberry Pi.

```bash
flash hypriotos-rpi-dirty.img.zip
BOARD=black-pearl.local make test-integration
```

This test works with any Docker Machine, so you do not need to create the
Vagrant box.


## Deployment

For maintainers of this project you can release a new version and deploy the
SD card image to GitHub releases with

```bash
TAG=v0.0.1 make tag
```

After that open the GitHub release of this version and fill it with relevant
changes and links to resolved issues.

## License

MIT - see the [LICENSE](./LICENSE) file for details.
