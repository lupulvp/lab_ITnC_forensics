require 'yaml'

config_file = YAML.load(File.read('config.yaml'))

STACK_NAME = config_file["stack_name"]
INTNET_NAME = config_file["intnet_name"]

WINDOWS10_VM = {
  "name" => "#{STACK_NAME}-Win10Enterprise22H2", 
  "box" => File.exist?(config_file["windows10_vm_box_local"]) ? config_file["windows10_vm_box_local"] : config_file["windows10_vm_box"],
  "version" => File.exist?(config_file["windows10_vm_box_local"]) ? nil : config_file["windows10_vm_box_version"],
  "username" => config_file["windows10_vm_username"], 
  "password" => config_file["windows10_vm_password"],
  "memory" => config_file["windows10_vm_memory"],
  "cpus" => config_file["windows10_vm_cpus"], 
  "vram" => 128,
  "active" => config_file["windows10_vm_active"],
}

WINSERVER22_VM = {
  "name" => "#{STACK_NAME}-WinServer2022-21H2", 
  "box" => File.exist?(config_file["winserver22_vm_box_local"]) ? config_file["winserver22_vm_box_local"] : config_file["winserver22_vm_box"],
  "version" => File.exist?(config_file["winserver22_vm_box_local"]) ? nil : config_file["winserver22_vm_box_version"],
  "username" => config_file["winserver22_vm_username"], 
  "password" => config_file["winserver22_vm_password"],
  "memory" => config_file["winserver22_vm_memory"],
  "cpus" => config_file["winserver22_vm_cpus"], 
  "vram" => 128,
  "active" => config_file["winserver22_vm_active"],
}

KALI_VM = {
  "name" => "#{STACK_NAME}-Kali", 
  "box" => File.exist?(config_file["kali_vm_box_local"]) ? config_file["kali_vm_box_local"] : config_file["kali_vm_box"],
  "version" => File.exist?(config_file["kali_vm_box_local"]) ? nil : config_file["kali_vm_box_version"],
  "username" => config_file["kali_vm_username"], 
  "password" => config_file["kali_vm_password"],
  "memory" => config_file["kali_vm_memory"],
  "cpus" => config_file["kali_vm_cpus"], 
  "vram" => 128,
  "active" => config_file["kali_vm_active"],
}


Vagrant.configure("2") do |config|

  # Win10Enterprise22H2
  if WINDOWS10_VM["active"] == true  
    config.vm.define WINDOWS10_VM["name"] do |win10|
      win10.vm.box = WINDOWS10_VM["box"]  
      if WINDOWS10_VM["version"] != nil
        win10.vm.box_version = WINDOWS10_VM["version"]
      end

      win10.vm.hostname = WINDOWS10_VM["name"]
      win10.vm.guest = :windows
      win10.vm.synced_folder ".", "/vagrant", disabled: true  # disable synced folder
      win10.vm.network :private_network, virtualbox__intnet: INTNET_NAME, auto_config: false

      win10.vm.communicator = "winrm"
      win10.winrm.basic_auth_only = true    
      win10.winrm.transport = :plaintext
      win10.winrm.username = WINDOWS10_VM["username"]
      win10.winrm.password = WINDOWS10_VM["password"]
      win10.vm.boot_timeout = 1200
      win10.winrm.timeout = 1200
      win10.winrm.retry_limit = 20

      win10.vm.provision "shell", inline: "echo 'Provisioning Win10Enterprise22H2 VM...'"
      win10.vm.provision "shell", path: "./res/windows10/install-chrome.ps1", privileged: false     
      win10.vm.provision "shell", path: "./res/windows10/configure-windows-network.ps1", privileged: true, args: "-adapterName 'Ethernet 2' -ip '10.100.0.105' -gateway '10.100.0.1' -dns '10.100.0.1'"
      win10.vm.provision "shell", path: "./res/windows10/provision.ps1", privileged: true
      win10.vm.provision "shell", inline: "echo 'Provisioning ended, rebooting...'"
      win10.vm.provision :reload

      win10.vm.provider "virtualbox" do |win10_vb, override|
        win10_vb.gui = true
        win10_vb.name = WINDOWS10_VM["name"]

        win10_vb.customize ["modifyvm", :id, "--memory", WINDOWS10_VM["memory"]]
        win10_vb.customize ["modifyvm", :id, "--cpus", WINDOWS10_VM["cpus"]]
        win10_vb.customize ["modifyvm", :id, "--vram", WINDOWS10_VM["vram"]]
        win10_vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
        win10_vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
      end
    end
  end

  
  # WinServer2022-21H2
  if WINSERVER22_VM["active"] == true
    config.vm.define WINSERVER22_VM["name"] do |win22|
      win22.vm.box = WINSERVER22_VM["box"]  
      if WINSERVER22_VM["version"] != nil
        win22.vm.box_version = WINSERVER22_VM["version"]
      end

      win22.vm.hostname = WINSERVER22_VM["name"]
      win22.vm.guest = :windows
      win22.vm.synced_folder ".", "/vagrant", disabled: true  # disable synced folder
      win22.vm.network :private_network, virtualbox__intnet: INTNET_NAME, auto_config: false

      win22.vm.communicator = "winrm"
      win22.winrm.basic_auth_only = true    
      win22.winrm.transport = :plaintext
      win22.winrm.username = WINSERVER22_VM["username"]
      win22.winrm.password = WINSERVER22_VM["password"]
      win22.vm.boot_timeout = 1200
      win22.winrm.timeout = 1200
      win22.winrm.retry_limit = 20

      win22.vm.provision "shell", inline: "echo 'Provisioning WinServer2022-21H2 VM...'"
      win22.vm.provision "shell", path: "./res/winserver22/install-chrome.ps1", privileged: false     
      win22.vm.provision "shell", path: "./res/winserver22/configure-windows-network.ps1", privileged: true, args: "-adapterName 'Ethernet 2' -ip '10.100.0.106' -gateway '10.100.0.1' -dns '10.100.0.1'"
      win22.vm.provision "shell", path: "./res/winserver22/provision.ps1", privileged: true
      win22.vm.provision "shell", inline: "echo 'Provisioning ended, rebooting...'"
      win22.vm.provision :reload

      win22.vm.provider "virtualbox" do |win22_vb, override|
        win22_vb.gui = true
        win22_vb.name = WINSERVER22_VM["name"]

        win22_vb.customize ["modifyvm", :id, "--memory", WINSERVER22_VM["memory"]]
        win22_vb.customize ["modifyvm", :id, "--cpus", WINSERVER22_VM["cpus"]]
        win22_vb.customize ["modifyvm", :id, "--vram", WINSERVER22_VM["vram"]]
        win22_vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
        win22_vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
      end
    end
  end
  
  # Kali
  if KALI_VM["active"] == true
    config.vm.define KALI_VM["name"] do |kali|
      kali.vm.box = KALI_VM["box"]  
      if KALI_VM["version"] != nil
        kali.vm.box_version = KALI_VM["version"]
      end

      kali.vm.hostname = KALI_VM["name"]
      kali.vm.synced_folder ".", "/vagrant", disabled: true  # disable synced folder
      kali.vm.network :private_network, virtualbox__intnet: INTNET_NAME, auto_config: false

      kali.ssh.username = KALI_VM["username"]
      kali.ssh.password = KALI_VM["password"]

      kali.vm.provision "shell", inline: "echo 'Provisioning Kali VM...'"
      kali.vm.provision "shell", path: "./res/kali/provision.sh", privileged: true
      kali.vm.provision "shell", inline: "echo 'Provisioning ended, rebooting...'"
      kali.vm.provision :reload

      kali.vbguest.auto_update = false if Vagrant.has_plugin?("vagrant-vbguest")

      kali.vm.provider "virtualbox" do |kali_vb, override|
        kali_vb.gui = true
        kali_vb.name = KALI_VM["name"]

        kali_vb.customize ["modifyvm", :id, "--memory", KALI_VM["memory"]]
        kali_vb.customize ["modifyvm", :id, "--cpus", KALI_VM["cpus"]]
        kali_vb.customize ["modifyvm", :id, "--vram", KALI_VM["vram"]]
        kali_vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
        kali_vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
      end
    end
  end


end
