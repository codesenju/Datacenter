$ansible = <<-SCRIPT
sudo yum -y install ansible
ansible --version
SCRIPT
$hostfiles = <<-SCRIPT
cat <<EOT >> /etc/hosts
192.168.255.2 servera.local servera
192.168.255.3 serverb.local serverb
EOT
SCRIPT
$docker = <<-SCRIPT
sudo yum -y update
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install -y docker-ce-18.09.0 docker-ce-cli-18.09.0 containerd.io
sudo systemctl start docker
sudo usermod -aG docker vagrant
sudo curl -L "https://github.com/docker/compose/releases/download/1.18.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
SCRIPT
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: $hostfiles
  config.vm.provision "shell", inline: $docker
  #config.ssh.private_key_path = "C:/Users/F5367204/.vagrant.d/insecure_private_key"
  config.vm.synced_folder "data/", "/syncdata"
  config.vm.boot_timeout = 400
  
  config.vm.define "servera" do |servera|
    servera.vm.box = "geerlingguy/centos7"
    servera.vm.hostname = "servera.local"
    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    servera.vm.network "private_network", ip: "192.168.255.2"
  #  config.vm.network "public_network", bridge: "Ethernet 2:"
    servera.vm.provision "shell", inline: $ansible
  end

  config.vm.define "serverb" do |serverb|
    serverb.vm.box = "geerlingguy/centos7"
    serverb.vm.hostname = "serverb.local"
    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    serverb.vm.network "private_network", ip: "192.168.255.3"
  end
end
