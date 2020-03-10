# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :inetRouter => {
        :box_name => "centos/7",
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
                   {ip: '192.168.2.1', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "dev1-net"},
                   {ip: '192.168.2.65', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "test1-net"},
                   {ip: '192.168.2.129', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "man1-net"},
                   {ip: '192.168.2.193', adapter: 5, netmask: "255.255.255.192", virtualbox__intnet: "hard1-net"},
                   {ip: '192.168.255.6', adapter: 6, netmask: "255.255.255.252", virtualbox__intnet: "router1-net"},
                ]
  },
  :Office1Server => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.2.2', adapter: 2, netmask: "255.255.255.192", virtualbox__intnet: "dev1-net"},
                ]
  },
  :Office2Router => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.1.1', adapter: 2, netmask: "255.255.255.128", virtualbox__intnet: "dev2-net"},
                   {ip: '192.168.1.129', adapter: 3, netmask: "255.255.255.192", virtualbox__intnet: "test2-net"},
                   {ip: '192.168.1.193', adapter: 4, netmask: "255.255.255.192", virtualbox__intnet: "hw2-net"},
                   {ip: '192.168.255.10', adapter: 5, netmask: "255.255.255.252", virtualbox__intnet: "router2-net"},
                ]
  },
  :Office2Server => {
        :box_name => "centos/7",
        :net => [
                   {ip: '192.168.1.2', adapter: 2, netmask: "255.255.255.128", virtualbox__intnet: "dev2-net"},
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
          yum install traceroute -y
        SHELL
        
        case boxname.to_s
        when "inetRouter"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            sysctl net.ipv4.conf.all.forwarding=1
            iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
            ip route add 192.168.0.0/16 via 192.168.255.2
            SHELL
        when "centralRouter"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
            systemctl restart network
            sysctl net.ipv4.conf.all.forwarding=1
            ip route add default via 192.168.255.1
            ip route add 192.168.2.0/24 via 192.168.255.6
            ip route add 192.168.1.0/24 via 192.168.255.10
            SHELL
        when "centralServer"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0 
            systemctl restart network
            ip route add default via 192.168.0.1
            SHELL
        when "Office1Router"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
            systemctl restart network
            sysctl net.ipv4.conf.all.forwarding=1
            ip route add default via 192.168.255.5
            SHELL
        when "Office1Server"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
            systemctl restart network
            ip route add default via 192.168.2.1
            SHELL
        when "Office2Router"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
            systemctl restart network
            sysctl net.ipv4.conf.all.forwarding=1
            ip route add default via 192.168.255.9
            SHELL
        when "Office2Server"
          box.vm.provision "shell", run: "always", inline: <<-SHELL
            echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
            systemctl restart network
            ip route add default via 192.168.1.1
            SHELL

        end

      end

  end
  
  
end
