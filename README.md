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


## Starting the Virtual Machines
Copy `config-template.yaml` to `config.yaml` and update the variables if needed.

In root directory run

```sh

$ vagrant up

```
Credentials:
- Window 10 VM `vagrant:vagrant`
- Windows Server 2022 VM `Administrator:Somepassword2023@#$`


### Notes
- adjust number of CPUs and amount of memory depending on your local resources in `config.yaml` file
- each VM can be deactivated from the configuration if it's not needed to be launched
- vagrant will check if the boxes are present in the `./Boxes` folder and if not it will pull them from the Vagrant cloud




# Building the vagrant boxes

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
$ certutil -hashfile ./Boxes//Win10Enterprise22H2_virtualbox.box SHA256
SHA256 hash of ./Boxes//Win10Enterprise22H2_virtualbox.box:
f85f1e7de920b53fb795cbd955c6503bd9515a585542f53beb8eb589a13c9d4c
CertUtil: -hashfile command completed successfully.
```

## Building the Windows Server 2022 21H2 VM (manual box creation)
Steps:
1. Download the ISO file from Microsoft: https://software-static.download.prss.microsoft.com/sg/download/888969d5-f34g-4e03-ac9d-1f9786c66749/SERVER_EVAL_x64FRE_en-us.iso
2. Create VirtaulBox VM with proper specs (CPUs, Memory) and use the ISO file to install. Go through the install wizard. Credentials `Administrator:Somepassword2023@#$` . Note: Do not use the unattended install, this didn't work for me.
3. Try to boot the VM and see if everything works as expected. Install the VirtualBox Guest Additions (recommended).
4. Configure the VM as per Vagrant docs: https://developer.hashicorp.com/vagrant/docs/boxes/base . Run in CMD not in PowerShell
```
reg add HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Policies\System /v EnableLUA /d 0 /t REG_DWORD /f /reg:64

winrm quickconfig -q
winrm set winrm/config/winrs @{MaxMemoryPerShellMB="512"}
winrm set winrm/config @{MaxTimeoutms="1800000"}
winrm set winrm/config/service @{AllowUnencrypted="true"}
winrm set winrm/config/service/auth @{Basic="true"}
sc config WinRM start= auto

netsh firewall add portopening TCP 5985 "Port 5985"
winrm set winrm/config/listener?Address=*+Transport=HTTP @{Port="5985"}

```
5. Package it as a Vagrant box with command `vagrant package --base vbox_vm_name --output box_name.box`
```
$ ./scripts/VBoxManage.sh list vms
"kali-linux-2023.2-virtualbox-amd64" {f43a94b9-4908-4cf7-bab9-4584b7c5473c}
"WindowsServer2022-21H2" {bcde8bb0-94fe-448a-919f-cc234c8f6b65}

$ vagrant package --base WindowsServer2022-21H2 --output ./Boxes/WinServer2022_virtualbox.box
==> WindowsServer2022-21H2: Exporting VM...
```
6. Calculate SHA256 so it can be uploaded
```
$ certutil -hashfile ./Boxes/WinServer2022_virtualbox.box SHA256
SHA256 hash of ./Boxes/WinServer2022_virtualbox.box:
a9a7d2ca3c1b96711759b1e11919b2b6f5483d7f3360169483644083e3e070c5
CertUtil: -hashfile command completed successfully.
```
7. Upload box to Vagrant Cloud






## Notes
- The Windows 10 Enterprise key was taken from [https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)?redirectedfrom=MSDN#windows-10](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj612867(v=ws.11)?redirectedfrom=MSDN#windows-10)

- - Windows Server 2022 Standard KMS key:  VDYBN-27WPP-V4HQT-9VMD4-VMK7H 
[https://learn.microsoft.com/en-us/windows-server/get-started/kms-client-activation-keys#windows-server-2022 ](https://learn.microsoft.com/en-us/windows-server/get-started/kms-client-activation-keys#windows-server-2022)

- Uploading boxes to Vagrant cloud [https://developer.hashicorp.com/vagrant/vagrant-cloud/boxes/create](https://developer.hashicorp.com/vagrant/vagrant-cloud/boxes/create)



## Useful Downloads
- Download Windows Server 2022 ISO file if you want to build the box locally [https://software-static.download.prss.microsoft.com/sg/download/888969d5-f34g-4e03-ac9d-1f9786c66749/SERVER_EVAL_x64FRE_en-us.iso](https://software-static.download.prss.microsoft.com/sg/download/888969d5-f34g-4e03-ac9d-1f9786c66749/SERVER_EVAL_x64FRE_en-us.iso) SHA256 `70ab5af6264c2f967dccafc16fd9a46b6f0b07a7`
- Download Windows Server 2022 VHD file if you want to import it directly to Virtualbox [https://software-static.download.prss.microsoft.com/pr/download/20348.169.amd64fre.fe_release_svc_refresh.210806-2348_server_serverdatacentereval_en-us.vhd](https://software-static.download.prss.microsoft.com/pr/download/20348.169.amd64fre.fe_release_svc_refresh.210806-2348_server_serverdatacentereval_en-us.vhd)
- Ubuntu 20.04.6 LTS (Focal Fossa) [https://releases.ubuntu.com/focal/](https://releases.ubuntu.com/focal/)

