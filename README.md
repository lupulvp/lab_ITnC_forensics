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


## Starting the Virtual Machines
Copy `config-template.yaml` to `config.yaml` and update the variables if needed.

In root directory run

```sh

$ vagrant up

```
Credentials:
- Window 10 VM `analyst:infected`

At this point you have a Windows 10 Enterprise 22H2 virtual machine

### Notes
- adjust number of CPUs and amount of memory depending on your local resources in `config.yaml` file
- vagrant will check if the boxes are present in the `./Boxes` folder and if not it will pull them from the Vagrant cloud