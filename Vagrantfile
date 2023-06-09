Vagrant.configure("2") do |config|
  config.vm.box = "debian/jessie64"
  config.ssh.insert_key = false
  config.vm.provision "shell", inline: <<-SHELL
    echo "Updating system"
    apt-get update
    apt-get dist-upgrade -y

    echo "Installing postgres"
    apt-get install -y postgresql-client-8.4 postgresql-8.4

    echo "Configuring and restarting PostgreSQL"
    echo 'listen_addresses = '"'"'*'"'" >> /etc/postgresql/8.4/main/postgresql.conf
    echo 'host    all             all             10.0.2.0/24            md5' >> /etc/postgresql/8.4/main/pg_hba.conf
    systemctl restart postgresql

    echo "Creating vagrant user and database"
    echo "CREATE ROLE vagrant CREATEDB CREATEROLE CREATEUSER LOGIN UNENCRYPTED PASSWORD 'vagrant'" | sudo -u postgres psql -a -f -
    echo "CREATE DATABASE vagrant OWNER vagrant" | sudo -u postgres psql -a -f -

    echo "Preparing for packaging"
    systemctl stop postgresql syslog.socket syslog
    killall dhclient
    apt-get install -y zerofree
    apt-get autoremove
    apt-get clean
    mount -o remount,ro /
    zerofree /dev/sda1
    systemctl poweroff
  SHELL
end
