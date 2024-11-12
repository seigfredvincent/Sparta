Vagrant.configure("2") do |config|
  config.vm.define "web_server" do |web|
    web.vm.box = "hashicorp/bionic64"
    web.vm.network "private_network", ip: "192.168.57.83"
    web.vm.hostname = "spartaWEB"
    web.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
    end
    web.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y apache2 rsyslog ufw lynis
      sudo ufw allow in from 192.168.57.81 to any port 80
      sudo ufw enable
    SHELL
  end

  config.vm.define "db_server" do |db|
    db.vm.box = "hashicorp/bionic64"
    db.vm.network "private_network", ip: "192.168.57.81"
    db.vm.hostname = "spartaDB"
    db.vm.provider "virtualbox" do |vb|
      vb.memory = 1024
    end
    db.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y mysql-server rsyslog ufw lynis      
      sudo ufw allow in from 192.168.57.83 to any port 3306
      # sudo ufw default deny incoming
      sudo ufw enable
    SHELL
  end
end
