# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :inetRouter => {
        :box_name => "centos/7",
        #:public => {:ip => '10.10.10.1', :adapter => 1},
        :net => [
                   {ip: '192.168.255.1', adapter: 2, netmask: "255.255.255.252", virtualbox__intnet: "irouter-net"},
                ]
  },
  :centralRouter => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.0.1', adapter: 2, netmask: "255.255.255.240", virtualbox__intnet: "dir-net"},
                   {ip: '192.168.0.33', adapter: 3, netmask: "255.255.255.240", virtualbox__intnet: "hard-net"},
                   {ip: '192.168.0.65', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "wifi-net"},
                   {ip: '192.168.255.2', adapter: 5, netmask: "255.255.255.252", virtualbox__intnet: "irouter-net"},
                   {ip: '192.168.255.5', adapter: 6, netmask: "255.255.255.252", virtualbox__intnet: "router1-net"},
                   {ip: '192.168.255.9', adapter: 7, netmask: "255.255.255.252", virtualbox__intnet: "router2-net"},
                ]
  },
  :centralServer => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.0.2', adapter: 2, netmask: "255.255.255.240", virtualbox__intnet: "dir-net"},
                ]
  },
  :Office1Router => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.2.1', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "dev-net"},
                   {ip: '192.168.2.65', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "test-net"},
                   {ip: '192.168.2.129', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "man-net"},
                   {ip: '192.168.2.193', adapter: 5, netmask: "255.255.255.192", virtualbox__intnet: "hard-net"},
                   {ip: '192.168.255.6', adapter: 6, netmask: "255.255.255.252", virtualbox__intnet: "router1-net"},
                ]
  },
  :Office1Server => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.2.2', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "dev-net"},
                ]
  },
  :Office2Router => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.1.1', adapter: 2, netmask: "255.255.255.128", virtualbox__intnet: "dev-net"},
                   {ip: '192.168.1.129', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "-net"},
                   {ip: '192.168.1.193', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "-net"},
                   {ip: '192.168.255.10', adapter: 5, netmask: "255.255.255.252", virtualbox__intnet: "router2-net"},
                ]
  },
  :Office2Server => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.1.2', adapter: 2, netmask: "255.255.255.128", virtualbox__intnet: "dev-net"},
                ]
  }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|

        box.vm.box = boxconfig[:box_name]
        box.vm.host_name = boxname.to_s

        boxconfig[:net].each do |ipconf|
          box.vm.network "private_network", ipconf
        end
        
        if boxconfig.key?(:public)
          box.vm.network "public_network", boxconfig[:public]
        end

        box.vm.provision "shell", inline: <<-SHELL
          mkdir -p ~root/.ssh
                cp ~vagrant/.ssh/auth* ~root/.ssh
        SHELL
        
        case boxname.to_s
        when "inetRouter"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            sysctl net.ipv4.conf.all.forwarding=1
            iptables -t nat -A POSTROUTING ! -d 192.168.0.0/16 -o eth0 -j MASQUERADE
            SHELL
        when "centralRouter"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            sysctl net.ipv4.conf.all.forwarding=1
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
            echo "GATEWAY=192.168.255.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
            systemctl restart network
            SHELL
        when "centralServer"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
            echo "GATEWAY=192.168.0.1" >> /etc/sysconfig/network-scripts/ifcfg-eth1
            systemctl restart network
            SHELL
        end

      end

  end
  
  
end
