Vagrant.configure("2") do |config|

    init_consul = %Q{
      if [ ! $(which consul) ]; then
        curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
        sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
        sudo apt update
        sudo apt install consul -y
        sudo mkdir -p /etc/consul.d
        sudo mkdir -p /var/lib/consul
        sudo cp /vagrant/consul-server/consul.service /usr/lib/systemd/system/
        sudo cp /vagrant/consul-server/consul.hcl /etc/consul.d/
        sudo chown consul:consul /usr/bin/consul
        sudo chown --recursive consul:consul /etc/consul.d
        sudo chown --recursive consul:consul /var/lib/consul
        sudo chmod 640 /etc/consul.d/consul.hcl
        sudo systemctl enable consul
        sudo systemctl start consul &
      fi
    }
    
    config.vm.define "consul-server" do |server|
      server.vm.box = "ubuntu/bionic64"
      server.vm.hostname = "consul-server"
      server.vm.network "private_network", ip: "192.168.50.21"
      server.vm.provision "shell", inline: init_consul
    end

    init_haproxy = %Q{
      if [ ! $(which haproxy) ]; then
        sudo add-apt-repository -y ppa:vbernat/haproxy-2.4
        sudo apt update
        sudo DEBIAN_FRONTEND=noninteractive apt install -y haproxy
  
        wget https://github.com/haproxytech/dataplaneapi/releases/download/v2.3.0/dataplaneapi_2.3.0_Linux_x86_64.tar.gz
        tar -zxvf dataplaneapi_2.3.0_Linux_x86_64.tar.gz
        sudo cp build/dataplaneapi /usr/local/bin/
        sudo chmod +x /usr/local/bin/dataplaneapi
  
        sudo cp /vagrant/haproxy/haproxy.cfg /etc/haproxy/
        sudo cp /vagrant/haproxy/dataplaneapi.yaml /etc/haproxy/
        sudo systemctl restart haproxy
      fi
    }
 
    config.vm.define "haproxy" do |server|
      server.vm.box = "ubuntu/bionic64"
      server.vm.hostname = "haproxy"
      server.vm.network "private_network", ip: "192.168.50.20"
      server.vm.provision "shell", inline: init_haproxy
    end



    init_webserver = %Q{
      if [ ! $(which consul) ]; then
        curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
        sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
        sudo apt update
        sudo apt install consul -y
        sudo mkdir -p /etc/consul.d
        sudo mkdir -p /var/lib/consul
        sudo cp /vagrant/webserver/consul.service /usr/lib/systemd/system/
        sudo cp /vagrant/webserver/consul.hcl /etc/consul.d/
        sudo chown consul:consul /usr/bin/consul
        sudo chown --recursive consul:consul /etc/consul.d
        sudo chown --recursive consul:consul /var/lib/consul
        sudo chmod 640 /etc/consul.d/consul.hcl
        sudo systemctl enable consul
        sudo systemctl start consul &
      fi  

      sudo apt install nginx -y
      consul services register /vagrant/webserver/web.json
    }

    config.vm.define "webserver1" do |server|
      server.vm.box = "ubuntu/bionic64"
      server.vm.hostname = "webserver1"
      server.vm.network "private_network", ip: "192.168.50.22"
      server.vm.provision "shell", inline: init_webserver
    end
   
end