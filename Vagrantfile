# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Hack to gain access to the --tags argument
ANSIBLE_TAGS=ENV['ANSIBLE_TAGS']

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "trusty64"

  config.vm.provider :virtualbox do |vb, override|
    override.vm.provision "ansible" do |ansible|
      ansible.playbook = "vagrant.yml"
      ansible.host_key_checking = false
      ansible.verbose = "vv"
      ansible.tags = ANSIBLE_TAGS
    end

    override.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"

    override.vm.network :private_network, ip: "192.168.33.15"
    vb.customize ["modifyvm", :id, "--name", "MITOC Trips", "--memory", "1024"]
  end

  config.vm.provider :aws do |aws, override|
    override.vm.box = "dummy"

    override.vm.provision "ansible" do |ansible|
      ansible.playbook = "production.yml"
      ansible.host_key_checking = false
      ansible.verbose = "vv"
      ansible.tags = ANSIBLE_TAGS
    end

    # These can be hardcoded, in env vars, or in `~/.aws/credentials`
    #aws.access_key_id = ""
    #aws.secret_access_key = ""

    aws.ami = "ami-fce3c696"
    aws.instance_type = "t2.micro"
    aws.region = "us-east-1"
    aws.security_groups = ["webserver"]

    # Explicit defaults
    aws.tenancy = "default"

    # This is a keypair used to access your instance with `vagrant ssh`
    # AWS must have this keypair configured for your account.
    # https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html
    aws.keypair_name = "vagrant"
    override.ssh.username = "ubuntu"
    override.ssh.private_key_path = "#{Dir.home}/vagrant.pem"
  end
end
