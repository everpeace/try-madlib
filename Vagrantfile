# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

#  config.vm.provider :virtualbox do |vb|
#    config.vm.box_url="https://dl.dropbox.com/u/7225008/Vagrant/CentOS-6.3-x86_64-minimal.box"
#    config.vm.box="cent-os-6.3-x86_64-minimal"
#    # Use VBoxManage to customize the VM. For example to change memory:
#    vb.customize ["modifyvm", :id, "--memory", "1024"]
#  end

  config.vm.provider :aws do |aws|
    config.vm.box_url = "https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box"
    config.vm.box = "ec2-dummy"
    aws.access_key_id     = ENV['AWS_ACCESS_KEY']
    aws.secret_access_key = ENV['AWS_SECRET_KEY']
    aws.ami = "ami-ea30ab83"
    aws.ssh_username = "vagrant"
    aws.keypair_name = "ENV['AWS_KEY_PAIR']"
    aws.ssh_private_key_path = "~/.ssh/#{aws.keypair_name}.pem"
    aws.instance_type = "t1.micro"
    aws.region = "us-east-1"
    aws.security_groups = [ 'ssh' ]
  end

  config.vm.provision :chef_solo do |chef|
    chef.add_recipe "yum"
    chef.add_recipe "postgresql"
    chef.add_recipe "postgresql::server"
    chef.json = {
      :postgresql => {
        :password => {
          :postgres => 'password'
        }
      }
    }
  end

# install madlib utility and dependencies
  config.vm.provision :shell, :inline => "sudo yum -y install postgresql-plpython"
  config.vm.provision :shell, :inline => "sudo yum -y install /vagrant/madlib-0.5-Linux.rpm --nogpgcheck"

# setup database
  config.vm.provision :shell, :inline => "sudo su -l postgres -c 'createdb madlibtest -O postgres;createlang plpgsql madlibtest'"

# install madlib to database
  config.vm.provision :shell, :inline => "PGPASSWORD='password' /usr/local/madlib/bin/madpack -p postgres -c postgres@localhost/madlibtest install"

end
