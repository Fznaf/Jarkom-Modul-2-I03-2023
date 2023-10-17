# Jarkom-Modul-2-I03-2023
Lapres Praktikum Jarkom Modul 2 2023

- M. Ghifari Taqiuddin - 5025211063
- Fauzan Ahmad Faisal - 5025211067

# No 1
<img width="881" alt="Screenshot 2023-10-17 at 19 27 48" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/3ef905aa-dbee-4d5b-a69a-2fa8dfcb5e5a">

We first set our network topology to fit the requirement in the question. Our group got topology no. 4 so we then arrange the topology to look as such in the no. 4 picture.

After we built our topology, we set it up so that every node can connect to the internet. We configure the network and uses our group's assigned prefix IP which is 10.60. After ensuring that every node can connect to the internet (we checked it by trying to ping google from each node) we can then move to number 2.

# No 2
We are tasked to set a main website on node arjuna with the access to arjuna.yyy.com where yyy is our group's name, in our case, yyy is i03. This means that we need to set up the website with the name arjuna.i03.com and also set up an alias which is www.arjuna.i03.com.

We first intall bind9 service on arjuna using
```c
apt-get update
  apt-get install bind9 -y
```
Then we move to the named.conf.local file

```
zone "arjuna.i03.com" {
        type master;
        file "/etc/bind/jarkom/arjuna.i03.com";
};
```

We then set up the website on DNSMaster-Yudhistira with IP address pointing to LB-Arjuna. We also set up the alias for the www.arjuna.i03.com.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.i03.com. root.arjuna.i03.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.i03.com.
@       IN      A       10.60.3.5
www     IN      CNAME   arjuna.i03.com.
```

Don't forget to restart the bind service using ```service bind9 restart```
After that we set the client so that it has Yudhistira's IP and can ping arjuna.i03.com

<img width="737" alt="Screenshot 2023-10-17 at 19 32 43" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/e1ea0ca2-bc84-4062-b314-253790cfd23c">


It can be seen here that when pinging arjuna.i03.com or www.arjuna.i03.com, the IP is the IP for LB-Arjuna

# No 3
We are tasked to again set a main website. However, this time it is with the access to abimanyu.yyy.com where yyy is our group's name, in our case, yyy is i03. This means that we need to set up the website with the name abimanyu.i03.com and also set up an alias which is www.abimanyu.i03.com.

We set up the named.conf.local like this,

```
zone "abimanyu.i03.com" {
        type master;
        file "/etc/bind/jarkom/abimanyu.i03.com";
};
```

We then set up the website on DNSMaster-Yudhistira. We also set up the alias for the www.abimanyu.i03.com.

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.i03.com. root.abimanyu.i03.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.i03.com.
@               IN      A       10.60.1.3 ; IP DNSMaster-Yudhistira
www             IN      CNAME   abimanyu.i03.com.
```

remember to restart the bind service using ```service bind9 restart```

After that we set the client so that it can ping abimanyu.i03.com

```
// resolv.conf
nameserver 10.60.1.3
```

Then, we ping abimanyu.i03.com and also www.abimanyu.i03.com to see if the setup succeed

<img width="737" alt="Screenshot 2023-10-17 at 19 32 59" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/d179d79b-834a-4077-a085-3544bb84d1be">

# No 4
The question asked us to make a subdomain on DNSMaster-Yudhistira for abimanyu.i03.com that has the name of parikesit.abimanyu.i03.com.
To do this, we configure the bind file of abimanyu as such,

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.i03.com. root.abimanyu.i03.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@               IN      NS      abimanyu.i03.com.
@               IN      A       10.60.1.3 ; IP DNSMaster-Yudhistira
www             IN      CNAME   abimanyu.i03.com.
parikesit       IN      A       10.60.3.2 ; Subdomain ke IP Abimanyu
ns1             IN      A       10.60.1.2 ; Delegasi ke DNSSlave-Werkudara
baratayuda      IN      NS      ns1
```

Then we restart the bind service using ```service bind9 restart```

After that we will try to ping it from client to see if the setup was succesfull

<img width="695" alt="Screenshot 2023-10-17 at 19 36 19" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/7fbe96b0-2095-4aaf-a0e3-c6ab8f4cdc43">


# No 5
We are told to make the reverse domain for abimanyu. The IP for abimanyu.i03.com is Yudhistira's IP, which is ```10.60.1.3``` that mean we can take the first 3 numbers and reverse the order. This means that the reverse uses ```1.60.1.in-addr.arpa```. We then set up the named.conf.local file on Yudhistira that includes ```1.60.1.in-addr.arpa```

```
zone "1.60.10.in-addr.arpa" {
        type master;
        file "/etc/bind/jarkom/1.60.10.in-addr.arpa";
};
```

Restart the bind service as always using ```service bind9 restart```

Then to test if the setup is succesfull we can use this in the client
```c
apt-get update
apt-get install dnsutils
```
We should also add
```c
host -t PTR 10.60.1.3
```
A success setup will resulted in this

<img width="695" alt="Screenshot 2023-10-17 at 19 37 24" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/8333b1e8-5273-4bf2-b77c-a8991ee60bd4">

# No 6
We need to turn Werkudara into a DNS slave for Yudhistira.
First, we need `allow-transfer` and `also-notify` configured
in Yudhistira and points to Werkudara's IP address

```
zone "abimanyu.i03.com" {
        type master;
        also-notify { 10.60.1.2; }; // IP DNSSlave-Werkudara
        allow-transfer { 10.60.1.2; }; // IP DNSSlave-Werkudara
        file "/etc/bind/jarkom/abimanyu.i03.com";
};
```

Then in Werkudara, we can do the same configuration, except this time we set the type to `slave`

```
zone "abimanyu.i03.com" {
    type slave;
    masters { 10.60.1.3; }; // IP DNSMaster-Yudhistira
    file "/var/lib/bind/abimanyu.i03.com";
};
```

This way, when Yudhistira isn't available, the slave will respond the request instead

# No 7
We create the subdomain rjp.baratayuda.abimanyu.i03.com in the Werkudara node, which already being delegated previously. To delegate a domain, we can use `allow-transfer`

```
zone "abimanyu.i03.com" {
        type master;
        also-notify { 10.60.1.2; }; // IP DNSSlave-Werkudara
        allow-transfer { 10.60.1.2; }; // IP DNSSlave-Werkudara
        file "/etc/bind/jarkom/abimanyu.i03.com";
};
```

The inside the Werkudara node

```
zone "baratayuda.abimanyu.i03.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.i03.com";
};
```

And for the DNS record

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.i03.com. root.baratayuda.abimanyu.i3.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.i03.com.
@       IN      A       10.60.1.2 ; DNSSlave-Werkudara
www     IN      CNAME   baratayuda.abimanyu.i03.com.
```

Pinging from Nakula client

<img width="695" alt="Screenshot 2023-10-17 at 19 47 13" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/f8484bcb-a7a3-47f2-a469-5304f82c6d78">

# No 8
We need to create rjp.baratayuda.abimanyu.i03.com. Edit the DNS record in Werkudara

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.i03.com. root.baratayuda.abimanyu.i3.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.i03.com.
@       IN      A       10.60.1.2 ; DNSSlave-Werkudara
www     IN      CNAME   baratayuda.abimanyu.i03.com.
rjp     IN      A       10.60.3.2 ; Abimanyu
```

Try pinging from client

<img width="695" alt="Screenshot 2023-10-17 at 19 43 20" src="https://github.com/Fznaf/Jarkom-Modul-2-I03-2023/assets/59758342/9fef272d-b94b-44c8-83d7-36ba94bde495">

# No 9

# No 10

We need to setup Arjuna node as a load-balancer using Nginx with the round-robin algorithm, with the Abimanyu, Prabukusuma, and Wisanggeni as the worker

``` # Default menggunakan Round Robin
upstream myweb  {
        server 10.60.3.2:8002; # Abimanyu
        server 10.60.3.3:8001; # Prabukusuma
        server 10.60.3.4:8003; # Wisanggeni
}

server {
        listen 80;
        server_name arjuna.i03.com;

        location / {
        proxy_pass http://myweb;
        }
}
```

This way, when we test the server using `lynx`, it will alternate between all the workers

# Bash commands that within each node:
- Pandudewanata:
```c
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.60.0.0/16
apt-get update
apt-get install nginx -y
```

- DNSMaster-Yudhistira:
```c
  echo "nameserver 192.168.122.1" > /etc/resolv.conf
  apt-get update
  apt-get install bind9 -y
  cp $HOME/named.conf.local /etc/bind/
  cp $HOME/named.conf.options /etc/bind/
  cp -r $HOME/jarkom /etc/bind/
  service bind9 restart
```

- DNSSlave-Werkudara
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
cp $HOME/named.conf.local /etc/bind/
cp $HOME/named.conf.options /etc/bind/
cp -r $HOME/baratayuda /etc/bind/
service bind9 restart
```

- Abimanyu
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y
cp jarkom /etc/nginx/sites-enabled/
```

- Prabukusuma
 ```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y
cp jarkom /etc/nginx/sites-enabled/
```

- Wisanggeni
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y
cp jarkom /etc/nginx/sites-enabled/
```

- LB-ARJUNA
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y
```

- Client-Nakula
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install dnsutils -y
echo "nameserver 10.60.1.3" > /etc/resolv.conf
echo "nameserver 10.60.1.2" >> /etc/resolv.conf
```

- Client-Sadewa
```c
echo "nameserver 192.168.122.1" > /etc/resolv.conf

apt-get update
apt-get install dnsutils -y
```
