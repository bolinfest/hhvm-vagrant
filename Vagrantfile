# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :forwarded_port, guest: 80, host: 8080
  config.vm.network :forwarded_port, guest: 8000, host: 8100
  config.vm.network :private_network, ip: "192.168.66.66"

  # Make ports available for nuclide-server.
  config.vm.network :forwarded_port, guest: 9090, host: 9090
  config.vm.network :forwarded_port, guest: 9091, host: 9091
  config.vm.network :forwarded_port, guest: 9092, host: 9092
  config.vm.network :forwarded_port, guest: 9093, host: 9093

  config.vm.provider :virtualbox do |vb|
    vb.name = "nuclide-remote-editing"
    vb.customize ["modifyvm", :id, "--memory", "2048"]
    vb.customize ["modifyvm", :id, "--ostype", "Ubuntu_64"]
  end

  config.vm.provision "shell", privileged: false, inline: <<-shell
    sudo apt-get update
    sudo apt-get install python-software-properties  -y --force-yes
    sudo add-apt-repository ppa:mapnik/boost
    sudo add-apt-repository ppa:nginx/stable
    wget -O - http://dl.hhvm.com/conf/hhvm.gpg.key | sudo apt-key add -
    echo deb http://dl.hhvm.com/ubuntu precise main | sudo tee /etc/apt/sources.list.d/hhvm.list
    sudo apt-get update
    sudo apt-get install nginx -y --force-yes
    sudo apt-get install hhvm -y --force-yes
    sudo apt-get install screen vim -y --force-yes
    debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password password pa$$'
    debconf-set-selections <<< 'mysql-server-5.5 mysql-server/root_password_again password pa$$'
    sudo apt-get install mysql-server -y --force-yes

    sudo chown vagrant /etc/hhvm
    sudo cp /vagrant/conf/config.hdf /etc/hhvm/my-config.hdf
    sudo cp /vagrant/conf/php.ini /etc/hhvm/my-php.ini
    sudo rm /etc/nginx/sites-enabled/default
    sudo cp /vagrant/conf/nginx-fastcgi /etc/nginx/sites-available/nginx-fastcgi
    sudo ln -s /etc/nginx/sites-available/nginx-fastcgi /etc/nginx/sites-enabled/nginx-fastcgi
    sudo service nginx restart
    sudo update-rc.d hhvm defaults

    sudo hhvm -m daemon -c /etc/hhvm/my-php.ini -v Eval.EnableXHP=1

    sudo apt-get install git -y --force-yes
    git clone https://github.com/facebook/watchman.git ~/watchman
    cd ~/watchman
    sudo apt-get install autoconf -y --force-yes
    sudo apt-get install automake -y --force-yes
    ./autogen.sh
    ./configure
    sudo apt-get install make -y --force-yes
    make
    sudo make install

    sudo apt-get install curl -y --force-yes
    curl https://raw.githubusercontent.com/creationix/nvm/v0.11.1/install.sh | bash
    source ~/.profile
    nvm install 0.12.0
    nvm alias default 0.12.0
    sudo ln -s `which node` /usr/bin/node

    cd
    wget --no-verbose http://flowtype.org/downloads/flow-linux64-latest.zip
    sudo apt-get install unzip -y --force-yes
    unzip flow-linux64-latest.zip
    rm flow-linux64-latest.zip
    sudo ln -s ~/flow/flow /usr/bin/flow

    git clone https://github.com/facebook/nuclide.git ~/nuclide
    cd ~/nuclide
    sudo apt-get install --reinstall python-pkg-resources -y --force-yes
    ./scripts/dev/setup --no-atom
  shell
end
