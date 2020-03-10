# Network
## Теоретическая часть
### Inet
#### Router
gateway 10.0.0.1
|eth#|ip|net|mask|broadcast|hosts|name|
|---|---|---|---|---|---|---|
|eth1|192.168.255.1|192.168.255.0/30|255.255.255.252|192.168.255.3|2|central|
### Central
#### Router
gateway 192.168.255.1
|eth#|ip|net|mask|broadcast|hosts|name|
|---|---|---|---|---|---|---|
|eth1|192.168.0.1|192.168.0.0/28|255.255.255.240|192.168.0.15|14|dir|
|eth2|192.168.0.33|192.168.0.32/28|255.255.255.240|192.168.0.47|14|hw|
|eth3|192.168.0.65|192.168.0.64/26|255.255.255.192|192.168.0.127|62|wifi|
|eth4|192.168.255.2|192.168.255.0/30|255.255.255.252|192.168.255.3|2|inet|
|eth5|192.168.255.5|192.168.255.4/30|255.255.255.252|192.168.255.7|2|office1|
|eth6|192.168.255.9|192.168.255.8/30|255.255.255.252|192.168.255.11|2|office2|
#### Server
gateway 192.168.0.1
|eth#|ip|net|mask|
|---|---|---|---|
|eth1|192.168.0.2|192.168.0.0/28|255.255.255.240|
### Office1
#### Router
gateway 192.168.255.5
|eth#|ip|net|mask|broadcast|hosts|name|
|---|---|---|---|---|---|---|
|eth1|192.168.2.1|192.168.2.0/26|255.255.255.192|192.168.2.63|62|dev|
|eth2|192.168.2.65|192.168.2.64/26|255.255.255.192|192.168.2.127|62|test|
|eth3|192.168.2.129|192.168.2.128/26|255.255.255.192|192.168.2.191|62|managers|
|eth4|192.168.2.193|192.168.2.192/26|255.255.255.192|192.168.2.255|62|hw|
|eth5|192.168.255.6|192.168.255.4/30|255.255.255.252|192.168.255.7|2|central|
#### Server
gateway 192.168.2.1
|eth#|ip|net|mask|
|---|---|---|---|
|eth1|192.168.2.2|192.168.2.0/26|255.255.255.192|
### Office2
gateway 192.168.255.9
|eth#|ip|net|mask|broadcast|hosts|name|
|---|---|---|---|---|---|---|
|eth1|192.168.1.1|192.168.1.0/25|255.255.255.128|192.168.1.127|126|dev|
|eth2|192.168.1.129|192.168.1.128/26|255.255.255.192|192.168.1.191|62|test|
|eth3|192.168.1.193|192.168.1.192/26|255.255.255.192|192.168.1.255|62|hw|
|eth4|192.168.255.10|192.168.255.8/30|255.255.255.252|192.168.255.11|2|central|
#### Server
gateway 192.168.1.1
|eth#|ip|net|mask|
|---|---|---|---|
|eth1|192.168.1.2|192.168.1.0/25|255.255.255.128|
### Свободные подсети
|net|broadcast|hosts|
|---|---|---|
|192.168.0.16/28|192.168.0.31|14|
|192.168.0.48/28|192.168.0.63|14|
|192.168.0.128/25|192.168.0.255|126|

## Практическая часть
Для настроек сетевых адаптеров в Vagrantfile использую адреса интерфейсов из теоретической части
в секции провижн, для всех кроме inetRouter, нужно отключить маршрут по умолчанию через 0 интерфейс
```bash
echo "DEFROUTE=no" >> /etc/sysconfig/network-scripts/ifcfg-eth0
```
включить форвардинг
```bash
sysctl net.ipv4.conf.all.forwarding=1
```
и добавить маршруты к подсетям на хостах
### inet
#### Router
```bash
ip route add 192.168.0.0/16 via 192.168.255.2
```
### central
#### Router
```bash
ip route add default via 192.168.255.1
ip route add 192.168.2.0/24 via 192.168.255.6
ip route add 192.168.1.0/24 via 192.168.255.10
```
#### Server
```bash
ip route add default via 192.168.0.1
```
### Office1
#### Router
```bash
ip route add default via 192.168.255.5
```
#### Server
```bash
ip route add default via 192.168.2.1
```
### Office2
#### Router
```bash
ip route add default via 192.168.255.9
```
#### Server
```bash
ip route add default via 192.168.1.1
```
## Результат
* Сервера видят друг друга
* Доступ во внешнюю сеть идет через inetRouter

```bash
ping Office1
[root@centralServer ~]# ping 192.168.2.2
PING 192.168.2.2 (192.168.2.2) 56(84) bytes of data.
64 bytes from 192.168.2.2: icmp_seq=1 ttl=62 time=0.947 ms
64 bytes from 192.168.2.2: icmp_seq=2 ttl=62 time=1.03 ms
64 bytes from 192.168.2.2: icmp_seq=3 ttl=62 time=1.00 ms
64 bytes from 192.168.2.2: icmp_seq=4 ttl=62 time=0.947 ms
64 bytes from 192.168.2.2: icmp_seq=5 ttl=62 time=1.05 ms
ping Office2
[root@centralServer ~]# ping 192.168.1.2
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
64 bytes from 192.168.1.2: icmp_seq=1 ttl=62 time=0.952 ms
64 bytes from 192.168.1.2: icmp_seq=2 ttl=62 time=1.00 ms
64 bytes from 192.168.1.2: icmp_seq=3 ttl=62 time=1.01 ms
64 bytes from 192.168.1.2: icmp_seq=4 ttl=62 time=1.03 ms
64 bytes from 192.168.1.2: icmp_seq=5 ttl=62 time=0.980 ms
ping inet
[root@centralServer ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=59 time=17.3 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=59 time=17.4 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=59 time=18.2 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=59 time=17.2 ms
64 bytes from 8.8.8.8: icmp_seq=5 ttl=59 time=17.1 ms
```
```bash
[root@Office1Server ~]# ping 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
64 bytes from 192.168.0.1: icmp_seq=1 ttl=63 time=0.612 ms
64 bytes from 192.168.0.1: icmp_seq=2 ttl=63 time=0.724 ms
64 bytes from 192.168.0.1: icmp_seq=3 ttl=63 time=0.757 ms
64 bytes from 192.168.0.1: icmp_seq=4 ttl=63 time=0.711 ms
64 bytes from 192.168.0.1: icmp_seq=5 ttl=63 time=0.661 ms
64 bytes from 192.168.0.1: icmp_seq=6 ttl=63 time=0.667 ms
[root@Office1Server ~]# ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_seq=1 ttl=62 time=1.04 ms
64 bytes from 192.168.1.1: icmp_seq=2 ttl=62 time=1.18 ms
64 bytes from 192.168.1.1: icmp_seq=3 ttl=62 time=1.02 ms
64 bytes from 192.168.1.1: icmp_seq=4 ttl=62 time=1.02 ms
64 bytes from 192.168.1.1: icmp_seq=5 ttl=62 time=0.890 ms
[root@Office1Server ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=57 time=33.5 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=57 time=32.7 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=57 time=32.9 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=57 time=17.6 ms
```
```bash
[root@Office2Server ~]# ping 192.168.0.1
PING 192.168.0.1 (192.168.0.1) 56(84) bytes of data.
64 bytes from 192.168.0.1: icmp_seq=1 ttl=63 time=0.533 ms
64 bytes from 192.168.0.1: icmp_seq=2 ttl=63 time=0.655 ms
64 bytes from 192.168.0.1: icmp_seq=3 ttl=63 time=0.663 ms
64 bytes from 192.168.0.1: icmp_seq=4 ttl=63 time=0.680 ms
64 bytes from 192.168.0.1: icmp_seq=5 ttl=63 time=0.766 ms
[root@Office2Server ~]# ping 192.168.2.1
PING 192.168.2.1 (192.168.2.1) 56(84) bytes of data.
64 bytes from 192.168.2.1: icmp_seq=1 ttl=62 time=1.01 ms
64 bytes from 192.168.2.1: icmp_seq=2 ttl=62 time=0.975 ms
64 bytes from 192.168.2.1: icmp_seq=3 ttl=62 time=1.03 ms
64 bytes from 192.168.2.1: icmp_seq=4 ttl=62 time=1.01 ms
[root@Office2Server ~]# ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=57 time=17.7 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=57 time=17.6 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=57 time=17.6 ms
64 bytes from 8.8.8.8: icmp_seq=4 ttl=57 time=20.4 ms
```
