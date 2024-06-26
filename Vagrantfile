# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "debian/bullseye64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  config.vm.network "private_network", ip: "192.168.33.16"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Disable the default share of the current code directory. Doing this
  # provides improved isolation between the vagrant box and your host
  # by making sure your Vagrantfile isn't accessable to the vagrant box.
  # If you use this you may want to enable additional shared subfolders as
  # shown above.
  # config.vm.synced_folder ".", "/vagrant", disabled: true

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get upgrade
    sudo apt-get install gunicorn3
    sudo apt install software-properties-common python-software-properties
    sudo add-apt-repository ppa:pypa/ppa
    sudo apt update
    sudo apt-get install -y python3-pip
    sudo apt-get install -y nginx ufw
    sudo ufw enable
    sudo ufw allow ssh
    sudo ufw allow 'Nginx Full'
    sudo ufw delete allow 'Nginx HTTP'
    sudo ufw allow 5000/tcp
    sudo ufw --force enable
    pip3 install pipenv
    PATH=$PATH:/home/vagrant/.local/bin 
    pipenv --version
    pip3 install python-dotenv
    sudo mkdir -p /var/www/prueba
    cd /var/www
    sudo chown -R vagrant:www-data /var/www/prueba
    sudo chmod -R 775 /var/www/prueba
    sudo cp -r /vagrant/.env /var/www/prueba
    cd /var/www/prueba
    pipenv shell
    pipenv install flask gunicorn
    sudo cp -r /vagrant/application.py /var/www/prueba
    sudo cp -r /vagrant/wsgi.py /var/www/prueba
    touch application.py wsgi.py
    pip install python-dotenv
    flask run --host '0.0.0.0'
    sudo cp -r /vagrant/flask_app.service /etc/systemd/system/
    # Hay que modificar y redirigir los puertos del virtual box a * Running on http://10.0.2.15:5000
    # which gunicorn - localizar la ruta o path de gunicorn - /home/vagrant/.local/share/virtualenvs/prueba-6ukuHTx7/bin/gunicorn
    cp -r /vagrant/app.conf /etc/nginx/sites-available/
    ln -fs /etc/nginx/sites-available/app.conf /etc/nginx/sites-enabled/
    sudo systemctl start nginx
    sudo systemctl daemon-reload
    sudo systemctl enable --now flask_app.service

  SHELL
end
