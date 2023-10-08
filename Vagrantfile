Vagrant.configure("2") do |config|

  config.vm.box = "ubuntu/jammy64"
  config.vm.box_version = "202310.1.0"
  config.vm.hostname = "inkoms"

  config.vm.network "forwarded_port", guest: 9140, host: 9140
  config.vm.network "forwarded_port", guest: 9141, host: 9141
  config.vm.network "public_network"

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "1024"
  end

  config.vm.define "inkoms" do |inkoms|
    inkoms.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y ca-certificates curl gnupg git
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpgz
      echo 'deb [arch='$(dpkg --print-architecture)' signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable' | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      
      sudo apt-get update
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

      sudo groupadd docker
      sudo usermod -aG docker vagrant
      docker run hello-world

      git clone https://github.com/inkoms/inkoms-postgresql.git
      cd inkoms-postgresql
      docker compose -p inkoms-posgesql up -d
    SHELL
  end

end
