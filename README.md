# linux-dz-15
Network

Office1  

Router  
def_gw 192.168.255.4  

eth2 
ip 192.168.2.1  
net 192.168.2.0/26  
mask 255.255.255.192  
bc 192.168.2.63  
hosts 62  

eth3  
ip 192.168.2.65  
net 192.168.2.64/26  
mask 255.255.255.192  
bc 192.168.2.127  
hosts 62  

eth4  
ip 192.168.2.129  
net 192.168.2.128/26  
mask 255.255.255.192  
bc 192.168.2.191  
hosts 62  

eth5  
ip 192.168.2.193  
net 192.168.2.192/26  
mask 255.255.255.192  
bc 192.168.2.255  
hosts 62  
  
eth6-to-central  
ip 192.168.255.5  
net 192.168.255.5/30  
mask 255.255.255.252  
bc 192.168.255.7  
hosts 2  
  
Server  
def_gw 192.168.2.1  
eth2  
ip 192.168.2.2/26  
mask 255.255.255.192  


Office2  
  
Router  
def_gw 192.168.255.9  
  
eth2  
ip 192.168.1.1  
net 192.168.1.0/25  
mask 255.255.255.128  
bc 192.168.1.27  
hosts 126  
  
eth3  
ip 192.168.1.129  
net 192.168.1.128/26  
mask 255.255.255.192  
bc 192.168.1.191  
hosts 62  
  
eth4  
ip 192.168.1.193  
net 192.168.1.192/26  
mask 255.255.255.192  
bc 192.168.1.255  
hosts 62  
  
eth5-to-central  
ip 192.168.255.10  
net 192.168.255.8/30  
mask 255.255.255.252  
bc 192.168.255.11  
hosts 2  
  
Server  
def_gw 192.168.1.1  
  
eth2  
ip 192.168.1.2/25  
mask 255.255.255.128  
  
Central  
  
Router  
def_gw 192.168.255.1  
eth2-to-dir  
ip 192.168.0.1  
net 192.168.0.0/28  
mask 255.255.255.240  
bc 192.168.0.15  
hosts 14  
  
eth3-to-hard  
ip 192.168.0.33  
net 192.168.0.32/28  
mask 255.255.255.240  
bc 192.168.0.47  
hosts 14  
  
eth4-to-wifi  
ip 192.168.0.65  
net 192.168.0.64/26  
mask 255.255.255.192  
bc 192.168.0.127  
hosts 62  
  
eth5-to-inet  
ip 192.168.255.2  
net 192.168.255.2/30  
mask 255.255.255.252  
bc 192.168.255.3  
hosts 2  
  
eth6-to-office-1  
ip 192.168.255.5  
net 192.168.255.4/30  
mask 255.255.255.252  
bc 192.168.255.7  
hosts 2  
  
eth7-to-office-2  
ip 192.168.255.9  
net 192.168.255.8/30  
mask 255.255.255.252  
bc 192.168.255.11  
hosts 2  


FREE  
192.168.0.16/28  
hosts 14  
192.168.0.48/28  
hosts 14  
192.168.0.128/25  
hosts 126  

