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

## Building the Windows10 VM (automatic box creation)
Install guest additions plugin and move into our working directory.
  
```sh

$ cd Packer

```
  
Now we build and configure the VM. This will download the Win10 ISO from Microsoft, boot it, and install the necessary tools to configure it.

```sh
$ ./packer build windows_10-packer.json

```

Upload box to Vagrant cloud
```
$ certutil -hashfile ./Win10Enterprise22H2_virtualbox.box SHA256
SHA256 hash of ./Win10Enterprise22H2_virtualbox.box:
43232685b4f6b41a841adf3bb7f3e51e89114f014301d008005dfea256884435
CertUtil: -hashfile command completed successfully.
```

## Building the Windows Server 2022 21H2 VM (manual box creation)
Steps:
1. Download the VHD file from Microsoft: https://software-static.download.prss.microsoft.com/pr/download/20348.169.amd64fre.fe_release_svc_refresh.210806-2348_server_serverdatacentereval_en-us.vhd
2. Create VirtaulBox VM with proper specs (CPUs, Memory) and use the VHD file as virtual disk. Go through the install wizard. Credentials `Administrator:Somepassword2023@#$`
3. Try to boot the VM and see if everything works as expected. Install the VirtualBox Guest Additions (recommended).
4. Configure the VM as per Vagrant docs: https://developer.hashicorp.com/vagrant/docs/boxes/base 
5. Package it as a Vagrant box 
```
vagrant package --base vbox_vm_name --output box_name.box
```
6. Calculate SHA256 so it can be uploaded
```
$ certutil -hashfile ./Win10Enterprise22H2_virtualbox.box SHA256
SHA256 hash of ./Win10Enterprise22H2_virtualbox.box:
43232685b4f6b41a841adf3bb7f3e51e89114f014301d008005dfea256884435
CertUtil: -hashfile command completed successfully.
```
7. Upload box to Vagrant Cloud

Optional: 
If importing the VHD file does not work then convert it into a VDI file and use it to start the VM. 
Eg:
```
VBoxManage clonehd 20348.169.amd64fre.fe_release_svc_refresh.210806-2348_server_serverdatacentereval_en-us.vhd WindowsServer2022.vdi --format VDI
0%...10%...20%...30%...40%...50%...60%...70%...80%...90%...100%
Clone medium created in format 'VDI'. UUID: e516f2ea-615e-4ff7-ac8f-26b6d2dc3487
```


## Starting the Virtual Machines
Copy `config-template.yaml` to `config.yaml` and update the variables if needed.

In root directory run

```sh

$ vagrant up

```
Credentials:
- Window 10 VM `vagrant:vagrant`
- Windows Server 2022 VM `Administrator:Somepassword2023@#$`

At this point you have a Windows 10 Enterprise 22H2 virtual machine

### Notes
- adjust number of CPUs and amount of memory depending on your local resources in `config.yaml` file
- vagrant will check if the boxes are present in the `./Boxes` folder and if not it will pull them from the Vagrant cloud



## Notes
- The Windows key was taken from [https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)?redirectedfrom=MSDN#windows-10](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)?redirectedfrom=MSDN#windows-10)

- Uploading boxes to Vagrant cloud [https://developer.hashicorp.com/vagrant/vagrant-cloud/boxes/create](https://developer.hashicorp.com/vagrant/vagrant-cloud/boxes/create)






## Downloads
Windows Server 2022 iso: https://software-static.download.prss.microsoft.com/sg/download/888969d5-f34g-4e03-ac9d-1f9786c66749/SERVER_EVAL_x64FRE_en-us.iso
```
$ certutil -hashfile ./SERVER_EVAL_x64FRE_en-us.iso 
SHA1 hash of ./SERVER_EVAL_x64FRE_en-us.iso:
70ab5af6264c2f967dccafc16fd9a46b6f0b07a7
CertUtil: -hashfile command completed successfully.
```

Windows Server 2022 vhd: https://software-static.download.prss.microsoft.com/pr/download/20348.169.amd64fre.fe_release_svc_refresh.210806-2348_server_serverdatacentereval_en-us.vhd




