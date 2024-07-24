# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # GitLab VM 설정
  config.vm.define :gitlab do |cfg|
  config.vm.define :runner |cfg|

    cfg.vm.box = "ubuntu/jammy64"
    cfg.vm.hostname = "runner.local"

    if Vagrant.has_plugin?("vagrant-vbguest")
      cfg.vbguest.auto_update = false
    end

    cfg.vm.network "forwarded_port", guest: 80, host: 8080
    cfg.vm.network "forwarded_port", guest: 22, host: 8022

    cfg.vm.network "private_network", ip: "192.168.33.44"
    cfg.vm.synced_folder ".", "/vagrant", type: "rsync", rsync__exclude: [".git/"]

    cfg.vm.provider "virtualbox" do |vb|
      # Display the VirtualBox GUI when booting the machine
      #vb.gui = true
      vb.name = "gitlab.local"
      # Customize the amount of memory on the VM:
      vb.memory = "4096"
    end

    cfg.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y curl openssh-server ca-certificates

      echo "postfix postfix/mailname string $HOSTNAME" | sudo debconf-set-selections
      echo "postfix postfix/main_mailer_type string 'Internet Site'" | sudo debconf-set-selections
      DEBIAN_FRONTEND=noninteractive sudo apt-get install -y postfix

      if [ ! -e /vagrant/gitlab-ce.deb ]; then
          wget --content-disposition -O /vagrant/gitlab-ce.deb https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/jammy/gitlab-ce_15.9.3-ce.0_amd64.deb/download.deb
      fi
      sudo dpkg -i /vagrant/gitlab-ce.deb

      sudo gitlab-ctl reconfigure
    SHELL
  end
end
