# TD - Python PRO
## Python pour les réseaux et la sécurité
##### Alexis BRONIARCZYK 14/01/2020
#

### 5. Interrogation du DNS avec Python :

##### Les prérequis:
```
docker network create 3-node_net-0 
git config --global http.sslverify false
git clone https://registry.iutbeziers.fr:11443/pouchou/Python3InDocker.git
cd Python3InDocker
docker-compose up -d
docker-compose exec pythondev bash 
```

###### 5.1 Utilisez le module Python socket afin de résoudre www.iutbeziers.fr en adresse IP :
```python
import socket
print(socket.gethostbyname('www.iutbeziers.fr'))
194.199.227.80
```

###### 5.2 Utilisez le module dnspython afin de résoudre www.iutbeziers.fr en adresse IP et réciproquement :
```python
# Résolution classique
student@f15d840bb523:~$ python3
>>> import dns.resolver
>>> for rdata in dns.resolver.query("www.iutbeziers.fr", 'A') :
...     print (rdata.address)
... 
194.199.227.80
```
```python
# Résolution inverse
>>> from dns import reversename, resolver
>>> rev_name = reversename.from_address('194.199.227.80')
>>> reversed_dns = str(resolver.query(rev_name,"PTR")[0])
>>> reversed_dns
'www.iutbeziers.fr.'
```
##### 5.3 Donnez la liste des NS, des MX du domaine iutbeziers.fr :
```python
# NS du domaine iutbeziers.fr
for NS in dns.resolver.query('iutbeziers.fr', 'NS'):
    print(NS.to_text())
```
```python
# MX du domaine iutbeziers.fr
for MX in dns.resolver.query('iutbeziers.fr', 'MX'):
    print(MX.to_text())
```

    scribe.iutbeziers.fr.
    10 mail.iutbeziers.fr.

##### 5.4 Retrouvez le resolver de votre container :
```python
mon_resolver = dns.resolver.Resolver()
print (mon_resolver.nameservers)
```

    ['127.0.0.11']

### 6. Gestion de l’adressage réseaux :

##### 6.1 Créer deux instances de IPNetwork avec les adresses réseaux des salles 202 et 203 :
####
Salle 202 :
```bash
>>> from netaddr import * 
>>> IP_Salle_202 = IPNetwork('10.202.0.0/16')
>>> print("Adresse réseau :",IP_Salle_202.network)
Adresse réseau : 10.202.0.0
>>> print("Adresse broadcast :",IP_Salle_202.broadcast)
Adresse broadcast : 10.202.255.255
>>> print("Masque de sous-réseau :",IP_Salle_202.netmask)
Masque de sous-réseau : 255.255.0.0
>>> print("Partie hôte de l'adresse IP :",IP_Salle_202.hostmask)
Partie hôte de l'adresse IP : 0.0.255.255
>>> print("Nombre d'adresses IP disponible :",IP_Salle_202.size)
Nombre d'adresses IP disponible : 65536
```
#####
Salle 203 :
```bash
>>> IP_Salle_203 = IPNetwork('10.203.0.0/16')
>>> print("Adresse réseau :",IP_Salle_203.network)
Adresse réseau : 10.203.0.0
>>> print("Adresse broadcast :",IP_Salle_203.broadcast)
Adresse broadcast : 10.203.255.255
>>> print("Masque de sous-réseau :",IP_Salle_203.netmask)
Masque de sous-réseau : 255.255.0.0
>>> print("Partie hôte de l'adresse IP :",IP_Salle_203.hostmask)
Partie hôte de l'adresse IP : 0.0.255.255
>>> print("Nombre d'adresses IP disponible :",IP_Salle_203.size)
Nombre d'adresses IP disponible : 65536

```
##### 6.2 Listez et comptez les adresses ip disponibles dans une salle en fonction du nombre de postes dans la salle (on prendra 20 postes par salle) :
On crée une liste qui contient les adresses de la salle 202 :
```python
liste_IP202 = []
for IP_202 in IPNetwork('10.202.0.0/16'):
    IP_202 = '%s' % IP_202
    liste_IP202.append(IP_202)
print("Nombre d'adresses IP disponible :",len(liste_IP202))
# il y a 65536 IP disponibles dans la salle 202
```
On crée une liste qui contient les adresses de la salle 203 :
```python
liste_IP203 = []
for IP_203 in IPNetwork('10.203.0.0/16'):
    IP_203 = '%s' % IP_203
    liste_IP203.append(IP_203)
print("Nombre d'adresses IP disponible :",len(liste_IP203))
# il y a 65536 IP disponibles dans la salle 203
```
Liste de 20 postes dans la salle 202 sur les 65536 disponibles :
```bash
salle_202 = [str(IP_202) for IP_202 in liste_IP202 for a in range(1,21) if IP_202 == ("10.202." + str(a) + ".1")]
print("Liste d'adresses IP disponibles dans la salle 202 :",salle_202)
Liste d'adresses IP disponibles dans la salle 202 : ['10.202.1.1', '10.202.2.1', '10.202.3.1', '10.202.4.1', '10.202.5.1', '10.202.6.1', '10.202.7.1', '10.202.8.1', '10.202.9.1', '10.202.10.1', '10.202.11.1', '10.202.12.1', '10.202.13.1', '10.202.14.1', '10.202.15.1', '10.202.16.1', '10.202.17.1', '10.202.18.1', '10.202.19.1', '10.202.20.1']
```
Liste de 20 postes dans la salle 203 sur les 65536 disponibles :
```bash
salle_203 = [str(IP_203) for IP_203 in liste_IP203 for a in range(1,21) if IP_203 == ("10.203." + str(a) + ".1")]
print("Liste d'adresses IP disponibles dans la salle 203 :",salle_203)
Liste d'adresses IP disponibles dans la salle 203 : ['10.203.1.1', '10.203.2.1', '10.203.3.1', '10.203.4.1', '10.203.5.1', '10.203.6.1', '10.203.7.1', '10.203.8.1', '10.203.9.1', '10.203.10.1', '10.203.11.1', '10.203.12.1', '10.203.13.1', '10.203.14.1', '10.203.15.1', '10.203.16.1', '10.203.17.1', '10.203.18.1', '10.203.19.1', '10.203.20.1']
```   
##### 6.3 Créez un nouvel objet Network en mergeant les réseaux des deux salles :
On créer une liste "ipmerge", à laquelle nous allons ajouter les 2 réseaux (202 et 203):
```python
>>> IP_202=IPNetwork('10.202.0.0/16')
>>> IP_203=IPNetwork('10.203.0.0/16')
>>> ipmerge = [] 
>>> ipmerge.append(IP_202)
>>> ipmerge.append(IP_203)
>>> ipmerge
[IPNetwork('10.202.0.0/16'), IPNetwork('10.203.0.0/16')]
```
Puis on merge ces réseaux :
```python
>>> mergednetwork = cidr_merge(ipmerge)    
>>> mergednetwork
[IPNetwork('10.202.0.0/15')]
```
##### 6.4 Listez les subnets /25 de ce nouveau réseau :
######
On récupère tous les subnets en /25:
```python
>>> subnets=list(IPNetwork('10.202.0.0/15').subnet(25))
>>> print (subnets)
[IPNetwork('10.202.0.0/25'), IPNetwork('10.202.0.128/25'), IPNetwork('10.202.1.0/25'), IPNetwork('10.202.1.128/25'), IPNetwork('10.202.2.0/25'),
...
IPNetwork('10.203.254.128/25'), IPNetwork('10.203.255.0/25'), IPNetwork('10.203.255.128/25')]
```

##### 6.5 Listez le supernet des réseaux des salles licence :
```python
>>> import pprint
>>> mergednetwork
>>> supernets = mergednetwork.supernet()
>>> pprint.pprint(supernets)
    [IPNetwork('0.0.0.0/0'),
     IPNetwork('0.0.0.0/1'),
     IPNetwork('0.0.0.0/2'),
     IPNetwork('0.0.0.0/3'),
     IPNetwork('0.0.0.0/4'),
     IPNetwork('8.0.0.0/5'),
     IPNetwork('8.0.0.0/6'),
     IPNetwork('10.0.0.0/7'),
     IPNetwork('10.0.0.0/8'),
     IPNetwork('10.128.0.0/9'),
     IPNetwork('10.192.0.0/10'),
     IPNetwork('10.192.0.0/11'),
     IPNetwork('10.192.0.0/12'),
     IPNetwork('10.200.0.0/13'),
     IPNetwork('10.200.0.0/14')]
```

##### 6.6 Vérifiez que les réseaux des salles de l’IUT suivent la RFC 1918 (adressage privée) :
####
Les plages d'adresses privées:
```bash
10.0.0.0  à   10.255.255.255  
172.16.0.0  à  172.31.255.255  
192.168.0.0  à  192.168.255.255 
```
La RFC est bien respecté car l'adresse de supernet "IPNetwork('10.0.0.0/8')", est comprise dans les plages d'adresses privées.

##### 6.7 En utilisant le package itertools et la fonction islice limitez le nombre de réseaux affichés à chaque print :
####
Ici on affiche les adresses IP de supernets avec des champs de 6 à 8.
```python
>>> from itertools import islice
>>> list(islice(supernet, 6, 8))
[IPNetwork('10.0.0.0/6'), IPNetwork('10.0.0.0/7')]
```


### 7. Utilisation de Python pour piloter des équipements réseaux : (15/01/2020)

##### Prérequis, Installer GNS3 sur sa VM :
```bash
root@debian:~# sudo apt-get install gcc make cmake
root@debian:~# sudo apt-get install libelf-dev root@debian:~# libpcap0.8-dev
root@debian:~# sudo apt-get install python3-setuptools
root@debian:~# sudo apt-get install --no-install-recommends python3-pip python3-dev python3-wheel
root@debian:~# sudo apt-get install python3-pyqt5 python3-pyqt5.qtsvg python3-pyqt5.qtwebsockets
root@debian:~# sudo pip3 install gns3-server
root@debian:~# sudo pip3 install gns3-gui
root@debian:~# sudo gns3
```
#### 7.1 Connexion d’un routeur Cisco sous GNS3 au réseau de la salle :
Après avoir mis en place l'architecture (le switch, le routeur et le cloud)
Je télécharge le script (https://moodle.didex.fr/moodle/mod/resource/view.php?id=18913) et je l'éxécute :

```bash
test@203-16:~$ bash getinet.sh 
pythondev:vethb75ed01
```
Puis je configure le routeur :
```cisco
R1#conf t
R1(config)#interface FastEthernet 0/0
R1(config-if)#ip address 172.18.0.3 255.255.0.0
R1(config-if)#no shutdown
R1(config-if)#exit
```
Puis on test la connectivitée :
```cisco
R1#      
*Mar  1 00:15:16.767: %SYS-5-CONFIG_I: Configured from console by console
R1#ping 172.18.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.18.0.2, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 8/11/16 ms
R1#ping 172.18.0.2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 172.18.0.2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/12/20 ms
```

#### 7.2 Utilisation du package telnetlib pour se connecter au routeur Cisco

Je configure l'accès telnet sur le routeur :
```cisco
R1#conf t
R1(config)#enable secret cisco
R1(config)#service password-encryption
R1(config)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#exit
```

Script pyhton qui permet de se connecter en telnet sur le container depuis mon container python

``` python
import sys
import telnetlib
import time

HOST = "172.18.0.3"
password = "cisco"
command = "sh ver"
term = "term len 0"

tn = telnetlib.Telnet(HOST)
cmd1 = "enable"
tn.set_debuglevel(1)
time.sleep(2)

tn.read_until(b"Password: ")
tn.write(password.encode('ascii') + b"\n")
time.sleep(2)
tn.write(cmd1.encode('ascii') + b"\n")
time.sleep(2)
tn.write(password.encode('ascii') + b"\n")
time.sleep(2)
tn.write(term.encode('ascii') + b"\n")
tn.write(command.encode('ascii') + b"\n")
time.sleep(2)
tn.write(b"\n")
time.sleep(2)
tn.write(b"\n")
time.sleep(2)
tn.write(b"exit\n")
lastpost = tn.read_all().decode('ascii')
print(lastpost)
op=open ("output.txt", "a").write(lastpost)
tn.close
```

Je lance le script :

```cisco
student@d59593327458:~/python$ python3 telnet1.py 
Telnet(172.18.0.3,23): recv b'\xff\xfb\x01\xff\xfb\x03\xff\xfd\x18\xff\xfd\x1f\r\n\r\nUser Access Verification\r\n\r\nPasswo'
Telnet(172.18.0.3,23): IAC WILL 1
Telnet(172.18.0.3,23): IAC WILL 3
Telnet(172.18.0.3,23): IAC DO 24
Telnet(172.18.0.3,23): IAC DO 31
...
...
R1#exit
```

Je lance le telnet :

```bash
student@d59593327458:~/python$ telnet 172.18.0.3
Trying 172.18.0.3...
Connected to 172.18.0.3.
Escape character is '^]'.


User Access Verification

Password: 
R1>
```

Puis j'utilise un script (telnet2.py) pour me connecter en ssh à mon routeur depuis mon container python :
```bash
import sys
import telnetlib
import time
HOST = "172.18.0.3"
password = "cisco"
command = "sh ver"
term = "term len 0"
conf = "configure terminal"
hostname = "hostname R1"
domain = "ip domain-name iutbeziers.fr"
crypto = "crypto key generate rsa general-keys modulus 2048"
newmodel = "aaa new-model"
username = "username admin secret cisco"
auth = "aaa authentication enable default line"
end = "end"
wr = "wr mem"
tn = telnetlib.Telnet(HOST)
cmd1 = "enable"
tn.set_debuglevel(1)
time.sleep(2)
tn.read_until(b"Password: ")
tn.write(password.encode('ascii') + b"\n")
time.sleep(2)
tn.write(cmd1.encode('ascii') + b"\n")
time.sleep(2)
tn.write(password.encode('ascii') + b"\n")
time.sleep(2)
tn.write(term.encode('ascii') + b"\n")
tn.write(command.encode('ascii') + b"\n")
time.sleep(2)
tn.write(conf.encode('ascii') + b"\n")
time.sleep(2)
tn.write(hostname.encode('ascii') + b"\n")
time.sleep(2)
tn.write(domain.encode('ascii') + b"\n")
time.sleep(2)
tn.write(crypto.encode('ascii') + b"\n")
time.sleep(2)
tn.write(newmodel.encode('ascii') + b"\n")
time.sleep(2)
tn.write(username.encode('ascii') + b"\n")
time.sleep(2)
tn.write(auth.encode('ascii') + b"\n")
time.sleep(2)
tn.write(end.encode('ascii') + b"\n")
time.sleep(2)
tn.write(b"\n")
time.sleep(2)
tn.write(b"\n")
time.sleep(2)
tn.write(wr.encode('ascii') + b"\n")
time.sleep(2)
tn.write(b"exit\n")
lastpost = tn.read_all().decode('ascii')
print(lastpost)
op=open ("output.txt", "a").write(lastpost)
tn.close
```

Je lance mon script python :

```bash
student@d59593327458:~/python$ python3 telnet2.py 
Telnet(172.18.0.3,23): recv b'\xff\xfb\x01\xff\xfb\x03\xff\xfd\x18\xff\xfd\x1f\r\n\r\nUser Access Verification\r\n\r\nPasswo'
Telnet(172.18.0.3,23): IAC WILL 1
Telnet(172.18.0.3,23): IAC WILL 3
Telnet(172.18.0.3,23): IAC DO 24
Telnet(172.18.0.3,23): IAC DO 31
Telnet(172.18.0.3,23): recv b'rd: '
Telnet(172.18.0.3,23): send b'cisco\n'
...
...
R1#
R1#wr mem
Building configuration...
[OK]
R1#exit
```

Maintenant on test , si l'on peut se connecter en ssh au routeur depuis le container :

```bash
student@d59593327458:~/python$ ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 -c aes128-cbc  admin@172.18.0.3
The authenticity of host '172.18.0.3 (172.18.0.3)' can't be established.
RSA key fingerprint is SHA256:o1aiPubvRz0AAfh9pKoRoD3hgp3GjKTIXjUAgoOc43c.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.18.0.3' (RSA) to the list of known hosts.
Password: 

R1>
```

### 7.3 Utilisation du package paramiko pour passer une commande sur un node système ou réseau



