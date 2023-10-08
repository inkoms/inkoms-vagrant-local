if File.exists?('.env')
  File.foreach('.env') do |line|
    if line =~ /^(.+)=(.+)$/
      ENV[$1] = $2.strip
    end
  end
end

Vagrant.configure("2") do |config|

  db_user = ENV['DB_USER']
  db_password = ENV['DB_PASSWORD']
  db_name = ENV['DB_NAME']
  db_port = ENV['DB_PORT']
  tz = ENV['TZ']
  pg_admin_email = ENV['PG_ADMIN_EMAIL']
  pg_admin_password = ENV['PG_ADMIN_PASSWORD']
  pg_admin_port = ENV['PG_ADMIN_PORT']

  config.vm.box = "ubuntu/jammy64"
  config.vm.hostname = "inkoms"
  config.vm.network "forwarded_port", guest: db_port, host: db_port
  config.vm.network "forwarded_port", guest: pg_admin_port, host: pg_admin_port

  config.vm.provider "virtualbox" do |vb|
    vb.cpus = "1"
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
      git clone https://github.com/inkoms/inkoms-postgresql.git || true
      cd inkoms-postgresql || true

      echo "DB_USER=#{db_user}" > .env
      echo "DB_PASSWORD=#{db_password}" >> .env
      echo "DB_NAME=#{db_name}" >> .env
      echo "DB_PORT=#{db_port}" >> .env
      echo "TZ=#{tz}" >> .env
      echo "PG_ADMIN_EMAIL=#{pg_admin_email}" >> .env
      echo "PG_ADMIN_PASSWORD=#{pg_admin_password}" >> .env
      echo "PG_ADMIN_PORT=#{pg_admin_port}" >> .env
      docker compose -p inkoms-postgresql up -d || true
      echo "-------------------------------------------------------------------------------"
      echo "Happy coding !! Access http://localhost:#{pg_admin_port}" from your browser
      echo "-------------------------------------------------------------------------------"
    SHELL
  end
end
