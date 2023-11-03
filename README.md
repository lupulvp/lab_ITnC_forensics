#  Lab for IT&C Forensics

## Preliminary required tasks before starting
- Clone the GitHub repository via the GitHub website or via the Git client (if installed)
- Download and install Vagrant [https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html) 
- Download Packer and move the binary to your Packer folder [https://packer.io/downloads.html](https://packer.io/downloads.html)
- Download and install Virtualbox [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
- (For Windows only) install Git SCM [https://git-scm.com/downloads](https://git-scm.com/downloads)

## Install vagrant plugins
```sh

$ vagrant plugin install vagrant-vbguest

$ vagrant plugin install vagrant-reload

```

## Building the Windows VM (automatic box creation)
Install guest additions plugin and move into our working directory.
  
```sh

$ cd Packer

```
  
Now we build and configure the VM. This will download the Win10 ISO from Microsoft, boot it, and install the necessary tools to configure it.

```sh
$ ./packer build windows_10-packer.json

```