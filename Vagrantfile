Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "inkoms"
  config.vm.network "forwarded_port", guest: 9140, host: 9140
  config.vm.network "forwarded_port", guest: 9141, host: 9141

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = 2
    vb.memory = "1024"
  end

  config.vm.define "inkoms" do |inkoms|
    inkoms.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update
      sudo apt-get install -y virtualbox-guest-dkms virtualbox-guest-utils virtualbox-guest-x11 lsb-release
      sudo apt-get install -y ca-certificates curl gnupg git
      sudo install -m 0755 -d /etc/apt/keyrings
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
      sudo chmod a+r /etc/apt/keyrings/docker.gpg
      echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      sudo apt-get update
      sudo apt-get install -y docker-ce docker-ce-cli containerd.io
      sudo groupadd docker || true
      sudo usermod -aG docker vagrant
      docker run hello-world || true
      git clone https://github.com/inkoms/inkoms-postgresql.git || true
      cd inkoms-postgresql || true
      docker compose -p inkoms-posgesql up -d || true
    SHELL
  end
end
