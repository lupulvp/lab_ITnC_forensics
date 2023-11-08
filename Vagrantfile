require 'yaml'

config_file = YAML.load(File.read('config.yaml'))

STACK_NAME = config_file["stack_name"]

WINDOWS10_VM = {
  "name" => "#{STACK_NAME}-Win10Enterprise22H2", 
  "box" => File.exist?("./Boxes/Win10Enterprise22H2_virtualbox.box") ? "./Boxes/Win10Enterprise22H2_virtualbox.box" : config_file["windows10_vm_box"],
  "version" => File.exist?("./Boxes/Win10Enterprise22H2_virtualbox.box") ? nil : config_file["windows10_vm_box_version"],
  "username" => "analyst", 
  "password" => "infected",
  "memory" => config_file["windows10_vm_memory"],
  "cpus" => config_file["windows10_vm_cpus"], 
  "vram" => 128
}


Vagrant.configure("2") do |config|
  # Win10Enterprise22H2
  config.vm.define WINDOWS10_VM["name"] do |win10|
    win10.vm.box = WINDOWS10_VM["box"]  
    if WINDOWS10_VM["version"] != nil
      win10.vm.box_version = WINDOWS10_VM["version"]
    end

    win10.vm.hostname = WINDOWS10_VM["name"]
    win10.vm.guest = :windows
    win10.vm.synced_folder ".", "/vagrant", disabled: true  # disable synced folder
    win10.vm.network :private_network, virtualbox__intnet: "itncforensiclab", auto_config: false

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
