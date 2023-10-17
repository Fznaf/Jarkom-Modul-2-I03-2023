# Jarkom-Modul-2-I03-2023
Lapres Praktikum Jarkom Modul 2 2023

- M. Ghifari Taqiuddin - 5025211063
 Fauzan Ahmad Faisal - 5025211067

# No 1
//image topologi

We first set our network topology to fit the requirement in the question. Our group got topology no. 4 so we then arrange the topology to look as such in the no. 4 picture.

//image topologi kita

After we built our topology, we set it up so that every node can connect to the internet. We configure the network and uses our group's assigned prefix IP which is 10.60. After ensuring that every node can connect to the internet (we checked it by trying to ping google from each node) we can then move to number 2.

# No 2
We are tasked to set a main website on node arjuna with the access to arjuna.yyy.com where yyy is our group's name, in our case, yyy is i03. This means that we need to set up the website with the name arjuna.i03.com and also set up an alias which is www.arjuna.i03.com.

We first intall bind9 service on arjuna using
```c
apt-get update
  apt-get install bind9 -y 
```
Then we move to the named.conf.local file
//image code named.conf
We then set up the website on DNSMaster-Yudhistira with IP address pointing to LB-Arjuna. We also set up the alias for the www.arjuna.i03.com.
//image code bind/jarkom/arjuna.i03.com
Don't forget to restart the bind service using ```service bind9 restart```
After that we set the client so that it has Yudhistira's IP and can ping arjuna.i03.com
//image ping arjuna
It can be seen here that when pinging arjuna.i03.com or www.arjuna.i03.com, the IP is the IP for LB-Arjuna

# No 3
We are tasked to again set a main website. However, this time it is with the access to abimanyu.yyy.com where yyy is our group's name, in our case, yyy is i03. This means that we need to set up the website with the name abimanyu.i03.com and also set up an alias which is www.abimanyu.i03.com.

We set up the named.conf.local like this,
//image code named.conf.local
We then set up the website on DNSMaster-Yudhistira. We also set up the alias for the www.abimanyu.i03.com.
//image code bind/jarkom/arjuna.i03.com
remember to restart the bind service using ```service bind9 restart```

After that we set the client so that it can ping abimanyu.i03.com
//image resolv.conf
Then, we ping abimanyu.i03.com and also www.abimanyu.i03.com to see if the setup succeed
//image ping abimanyu

# No 4
The question asked us to make a subdomain on DNSMaster-Yudhistira for abimanyu.i03.com that has the name of parikesit.abimanyu.i03.com.
To do this, we configure the bind file of abimanyu as such,

//image bind/jarkom/abimanyu.i03.com

Then we restart the bind service using ```service bind9 restart```

After that we will try to ping it from client to see if the setup was succesfull

//image ping parikesit

# No 5
We are told to make the reverse domain for abimanyu. The IP for abimanyu.i03.com is Yudhistira's IP, which is ```10.60.1.3``` that mean we can take the first 3 numbers and reverse the order. This means that the reverse uses ```1.60.1.in-addr.arpa```. We then set up the named.conf.local file on Yudhistira that includes ```1.60.1.in-addr.arpa```

//image named.conf.local

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

//image setelah host -t PTR 10.60.1.3

# Bash commands that texist within each node:
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

