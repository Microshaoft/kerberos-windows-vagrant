# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "dc" do |cfg|
    cfg.vm.box = "kensykora/windows_2012_r2_standard"
    cfg.vm.hostname = "dc"

    # use the plaintext WinRM transport and force it to use basic authentication.
    # NB this is needed because the default negotiate transport stops working
    #    after the domain controller is installed.
    #    see https://groups.google.com/forum/#!topic/vagrant-up/sZantuCM0q4
    cfg.winrm.transport = :plaintext
    cfg.winrm.basic_auth_only = true

    cfg.vm.communicator = "winrm"
    cfg.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    cfg.vm.network :private_network, ip: "192.168.39.2", gateway: "192.168.39.1"
    cfg.vm.provision "windows-sysprep"
    cfg.vm.provision "shell", path: "scripts/fix-second-network.ps1", privileged: false, args: "192.168.39.2"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false

    cfg.vm.provider "vmware_fusion" do |v, override|
      v.memory = 768
      v.cpus = 1
      v.gui = true
    end

    cfg.vm.provider "virtualbox" do |vb, override|
      vb.gui = true
      vb.customize ["modifyvm", :id, "--memory", 768]
      vb.customize ["modifyvm", :id, "--cpus", 1]
      vb.customize ["modifyvm", :id, "--vram", "32"]
      vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
    end
  end

  config.vm.define "web", autostart: false do |cfg|
    cfg.vm.box = "kensykora/windows_2012_r2_standard"
    cfg.vm.hostname = "web"

    cfg.vm.communicator = "winrm"
    cfg.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 80, host: 8080, id: "http", auto_correct: true
    cfg.vm.network :private_network, ip: "192.168.39.4", gateway: "192.168.39.1", dns: "192.168.39.2"
    cfg.vm.provision "windows-sysprep"

    cfg.vm.provision "shell", path: "scripts/fix-second-network.ps1", privileged: false, args: "-ip 192.168.39.4 -dns 192.168.39.2"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false
    cfg.vm.provision "shell", path: "scripts/increase-tcp-num-connections.ps1", privileged: false
    cfg.vm.provision "shell", path: "scripts/install-chocolatey.ps1", privileged: false
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "scripts/enable-kerberos-tomcat.ps1", privileged: false
    cfg.vm.provision "shell", path: "scripts/start-tomcat.ps1", privileged: false
    cfg.vm.provision "shell", path: "scripts/start-jetty.ps1", privileged: false

    cfg.vm.provider "virtualbox" do |vb, override|
      vb.gui = true
      vb.customize ["modifyvm", :id, "--memory", 1024]
      vb.customize ["modifyvm", :id, "--cpus", 2]
      vb.customize ["modifyvm", :id, "--vram", "32"]
      vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
    end
  end


  config.vm.define "client", autostart: false do |cfg|
    cfg.vm.box = "kensykora/windows_2012_r2_standard"
    cfg.vm.hostname = "client"

    cfg.vm.communicator = "winrm"
    cfg.vm.network :forwarded_port, guest: 5985, host: 5985, id: "winrm", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 22, host: 2222, id: "ssh", auto_correct: true
    cfg.vm.network :forwarded_port, guest: 3389, host: 3389, id: "rdp", auto_correct: true
    cfg.vm.network :private_network, ip: "192.168.39.9", gateway: "192.168.39.1"

    cfg.vm.provision "shell", path: "scripts/fix-second-network.ps1", privileged: false, args: "-ip 192.168.39.9 -dns 192.168.39.2"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "scripts/provision.ps1", privileged: false
    cfg.vm.provision "reload"
    cfg.vm.provision "shell", path: "scripts/enable-client-user.ps1", privileged: false
    cfg.vm.provision "reload"


    cfg.vm.provider "virtualbox" do |vb, override|
      vb.gui = true
      vb.customize ["modifyvm", :id, "--memory", 768]
      vb.customize ["modifyvm", :id, "--cpus", 1]
      vb.customize ["modifyvm", :id, "--vram", "32"]
      vb.customize ["modifyvm", :id, "--clipboard", "bidirectional"]
      vb.customize ["setextradata", "global", "GUI/SuppressMessages", "all" ]
    end
  end
end