#Summary: 

Here's learning project with HAProxy+keepalived floating IP.
There're Vagrantfile. It describes 4 VMs.

#List of VMs:

HAProxy equipment: 
- haproxy1.lab.int: IP LAN: 10.10.100.10, IP WAN: 192.168.50.100; 
- haproxy2.lab.int: IP LAN: 10.10.100.9, IP WAN: 192.168.50.99;
- HAProxy floating IP: 192.168.50.150;

Just 2 Web-servers:
- web1.lab.int: IP LAN: 10.10.100.110, processes site: web1.lab.int;
- web2.lab.int; IP LAN: 10.10.100.111,  processes site: web2.lb.lab;

#Describing processes:
HAProxy configs contain rules that processes html requests to web1.lab.int or web2.lab.int.
Keepalived works on haproxy1.lab.int and haproxy2.lab.int and provides float IP: 192.168.50.150 for interface eht2 on each VM.
If haproxy1.lab.int is available: float IP runs on it, else haproxy2.lab.int takes froat IP and enable HAProxy service.

