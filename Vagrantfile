# -*- mode: ruby -*-
# vi: set ft=ruby :

$rootScript = <<SCRIPT
  echo -e "\n========== Provisioning as $USER \n"
  cd /home/vagrant

  USER=etherpad
  GROUP=etherpad
  INSTALLDIR=/var/lib/etherpad-lite
  DBNAME=etherpad
  DBUSER=etherpad
  DBPASSWD=etherpad


  echo -e "\n========== Updating and installing basic packages \n"
  sudo apt-get update
  sudo apt-get install -y vim gzip git curl python libssl-dev pkg-config build-essential debconf-utils

  echo -e "\n========== Installing Libreoffice for PDF export \n"
  sudo apt-get install -y libreoffice-core

  echo -e "\n========== Installing MySQL \n"
  echo "mysql-server mysql-server/root_password password $DBPASSWD" | debconf-set-selections
  echo "mysql-server mysql-server/root_password_again password $DBPASSWD" | debconf-set-selections
  sudo apt-get install -y mysql-server

  echo -e "\n========== Setting up our MySQL user and db \n"
  mysql -uroot -p$DBPASSWD -e "CREATE DATABASE $DBNAME"
  mysql -uroot -p$DBPASSWD -e "grant all privileges on $DBNAME.* to '$DBUSER'@'localhost' identified by '$DBPASSWD'"

  echo -e "\n========== Installing NodeJS \n"
  curl -sL https://deb.nodesource.com/setup_5.x | bash -
  sudo apt-get install -y nodejs

  echo -e "\n========== Cloning etherpad \n"
  git clone https://github.com/ether/etherpad-lite.git $INSTALLDIR
  mv /home/vagrant/uploads/settings.json $INSTALLDIR/settings.json

  echo -e "\n========== Creating etherpad user \n"
  groupadd -r $GROUP
  useradd -r -g $GROUP -G $GROUP -m $USER -s /bin/bash
  sudo chown -R $USER:$GROUP $INSTALLDIR


  echo -e "\n========== Creating systemd unit \n"
  mv /home/vagrant/uploads/etherpad-lite.service /etc/systemd/system/etherpad-lite.service
  sudo systemctl daemon-reload
  sudo systemctl enable etherpad-lite
  sudo systemctl start etherpad-lite

  echo -e "\n========== DONE \n"

SCRIPT


Vagrant.configure(2) do |config|

  config.vm.box = "debian/jessie64"
  config.vm.network "forwarded_port", guest: 9001, host: 9001

  config.vm.provision "file", source: "settings.json", destination: "uploads/settings.json"
  config.vm.provision "file", source: "etherpad-lite.service", destination: "uploads/etherpad-lite.service"
  config.vm.provision "shell", inline: $rootScript

end
