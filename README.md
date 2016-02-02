# docker-virtualbox-nat-macosx
Add/delete NAT rules based on published ports in docker container. Now you can
access container ports using http://127.0.0.1:3000 (or any public IP on MAC)
instead of http://docker-vm-ip:3000.

Assumptions:
- Docker VM name is 'default'

Requirements:
- Install docker-py (if you use Docker < 2.0 install with --version 1.2.3)
- Run this script in Docker shell

bash-3.2$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                                                                                                                                                                                                       NAMES
4ec92e48c401        juniper/j-oracle    "/sbin/my_init"     8 days ago          Up 14 hours         0.0.0.0:80->80/tcp, 0.0.0.0:3000->3000/tcp, 0.0.0.0:8083->8083/tcp, 0.0.0.0:50000->50000/udp, 50000/tcp, 50010/tcp, 0.0.0.0:50010->50010/udp, 0.0.0.0:8086->8086/tcp, 0.0.0.0:50020->50020/udp, 50020/tcp   j-oracle_con

bash-3.2$ VBoxManage showvminfo "default" | grep Rule
NIC 1 Rule(0):   name = ssh, protocol = tcp, host ip = 127.0.0.1, host port = 60060, guest ip = , guest port = 22

bash-3.2$ python docker-install-pat-macosx.py -c j-oracle_con -a add
URL: https://192.168.99.100:2376
NATRULE: udp-port50010,udp,,50010,,50010
NATRULE: tcp-port8086,tcp,,8086,,8086
NATRULE: udp-port50000,udp,,50000,,50000
NATRULE: tcp-port8083,tcp,,8083,,8083
NATRULE: tcp-port3000,tcp,,3000,,3000
NATRULE: tcp-port80,tcp,,80,,80
NATRULE: udp-port50020,udp,,50020,,50020

bash-3.2$ VBoxManage showvminfo "default" | grep Rule
NIC 1 Rule(0):   name = ssh, protocol = tcp, host ip = 127.0.0.1, host port = 60060, guest ip = , guest port = 22
NIC 1 Rule(1):   name = tcp-port3000, protocol = tcp, host ip = , host port = 3000, guest ip = , guest port = 3000
NIC 1 Rule(2):   name = tcp-port80, protocol = tcp, host ip = , host port = 80, guest ip = , guest port = 80
NIC 1 Rule(3):   name = tcp-port8083, protocol = tcp, host ip = , host port = 8083, guest ip = , guest port = 8083
NIC 1 Rule(4):   name = tcp-port8086, protocol = tcp, host ip = , host port = 8086, guest ip = , guest port = 8086
NIC 1 Rule(5):   name = udp-port50000, protocol = udp, host ip = , host port = 50000, guest ip = , guest port = 50000
NIC 1 Rule(6):   name = udp-port50010, protocol = udp, host ip = , host port = 50010, guest ip = , guest port = 50010
NIC 1 Rule(7):   name = udp-port50020, protocol = udp, host ip = , host port = 50020, guest ip = , guest port = 50020

bash-3.2$ python docker-install-pat-macosx.py -c j-oracle_con -a delete
URL: https://192.168.99.100:2376

bash-3.2$ VBoxManage showvminfo "default" | grep Rule
NIC 1 Rule(0):   name = ssh, protocol = tcp, host ip = 127.0.0.1, host port = 60060, guest ip = , guest port = 22
