Vagrant.configure("2") do |config|
  host_ip = `hostname -I | awk '{print $1}'`.strip

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
      # Allow ssh from vagrant
      sudo ufw allow 2222
      
      # Set UFW default policies
      sudo ufw default deny incoming
      sudo ufw default allow outgoing

      # Allow only the Web Server to connect on MySQL 
      sudo ufw allow in from 192.168.57.83 to any port 3306
      
      #Enable UFW
      sudo ufw enable

      export VAULT_ADDR='http://#{host_ip}:8200'
      export VAULT_TOKEN='vault-jenkins'

      # Retrieve MySQL credentials from Vault
      MYSQL_USER=$(vault kv get -field=username /secrets/creds/vagrant)
      MYSQL_PASS=$(vault kv get -field=password /secrets/creds/vagrant)

      # Secure MySQL and create a test user
      sudo mysql -e "CREATE USER '$MYSQL_USER'@'192.168.57.83' IDENTIFIED BY '$MYSQL_PASS';"
      sudo mysql -e "GRANT ALL PRIVILEGES ON *.* TO '$MYSQL_USER'@'192.168.57.83';"
    SHELL
  end
end
