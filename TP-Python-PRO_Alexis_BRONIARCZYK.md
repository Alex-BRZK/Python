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

###### 1. Utilisez le module Python socket afin de résoudre www.iutbeziers.fr en adresse IP :
```python
import socket
print(socket.gethostbyname('www.iutbeziers.fr'))
194.199.227.80
```

###### 2. Utilisez le module dnspython afin de résoudre www.iutbeziers.fr en adresse IP et réciproquement :
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
##### 3. Donnez la liste des NS, des MX du domaine iutbeziers.fr :
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

##### 4. Retrouvez le resolver de votre container.
```python
mon_resolver = dns.resolver.Resolver()
print (mon_resolver.nameservers)
```

    ['127.0.0.11']

### 6. Gestion de l’adressage réseaux :


##### 1. Créer deux instances de IPNetwork avec les adresses réseaux des salles 202 et 203 :
```python
# Salle 202
from netaddr import * 
IP_Salle_202 = IPNetwork('10.202.0.0/16')
print("Adresse réseau :",IP_Salle_202.network)
print("Adresse broadcast :",IP_Salle_202.broadcast)
print("Masque de sous-réseau :",IP_Salle_202.netmask)
print("Partie hôte de l'adresse IP :",IP_Salle_202.hostmask)
print("Nombre d'adresses IP disponible :",IP_Salle_202.size)

print('\n')

# Salle 203
IP_Salle_203 = IPNetwork('10.203.0.0/16')
print("Adresse réseau :",IP_Salle_203.network)
print("Adresse broadcast :",IP_Salle_203.broadcast)
print("Masque de sous-réseau :",IP_Salle_203.netmask)
print("Partie hôte de l'adresse IP :",IP_Salle_203.hostmask)
print("Nombre d'adresses IP disponible :",IP_Salle_203.size)
```

    Adresse réseau : 10.202.0.0
    Adresse broadcast : 10.202.255.255
    Masque de sous-réseau : 255.255.0.0
    Partie hôte de l'adresse IP : 0.0.255.255
    Nombre d'adresses IP disponible : 65536
    
    
    Adresse réseau : 10.203.0.0
    Adresse broadcast : 10.203.255.255
    Masque de sous-réseau : 255.255.0.0
    Partie hôte de l'adresse IP : 0.0.255.255
    Nombre d'adresses IP disponible : 65536


###### 2. Listez et comptez les adresses ip disponibles dans une salle en fonction du nombre de postes dans la salle (on prendra 20 postes par salle) :

```python
# Salle 202
liste_IP202 = []
for IP_202 in IPNetwork('10.202.0.0/16'):
    IP_202 = '%s' % IP_202
    liste_IP202.append(IP_202)
print("Nombre d'adresses IP disponible :",len(liste_IP202))
# Salle 203
liste_IP203 = []
for IP_203 in IPNetwork('10.203.0.0/16'):
    IP_203 = '%s' % IP_203
    liste_IP203.append(IP_203)
print("Nombre d'adresses IP disponible :",len(liste_IP203))

print('\n')

# Liste de 20 postes dans la salle 202
salle_202 = [str(IP_202) for IP_202 in liste_IP202 for a in range(1,21) if IP_202 == ("10.202." + str(a) + ".1")]
print("Liste d'adresses IP disponibles dans la salle 202 :",salle_202)

print('\n')

# Liste de 20 postes dans la salle 203
salle_203 = [str(IP_203) for IP_203 in liste_IP203 for a in range(1,21) if IP_203 == ("10.203." + str(a) + ".1")]
print("Liste d'adresses IP disponibles dans la salle 203 :",salle_203)
```

    Nombre d'adresses IP disponible : 65536
    Nombre d'adresses IP disponible : 65536
    
    
    Liste d'adresses IP disponibles dans la salle 202 : ['10.202.1.1', '10.202.2.1', '10.202.3.1', '10.202.4.1', '10.202.5.1', '10.202.6.1', '10.202.7.1', '10.202.8.1', '10.202.9.1', '10.202.10.1', '10.202.11.1', '10.202.12.1', '10.202.13.1', '10.202.14.1', '10.202.15.1', '10.202.16.1', '10.202.17.1', '10.202.18.1', '10.202.19.1', '10.202.20.1']
    
    
    Liste d'adresses IP disponibles dans la salle 203 : ['10.203.1.1', '10.203.2.1', '10.203.3.1', '10.203.4.1', '10.203.5.1', '10.203.6.1', '10.203.7.1', '10.203.8.1', '10.203.9.1', '10.203.10.1', '10.203.11.1', '10.203.12.1', '10.203.13.1', '10.203.14.1', '10.203.15.1', '10.203.16.1', '10.203.17.1', '10.203.18.1', '10.203.19.1', '10.203.20.1']


##### 3. Créez un nouvel objet Network en mergeant les réseaux des deux salles :
```python


```

##### 4. Listez les subnets /25 de ce nouveau réseau :
```python


```

##### 5. Listez le supernet des réseaux des salles licence :
```python


```

##### 6. Vérifiez que les réseaux des salles de l’IUT suivent la RFC 1918 (adressage privée) :
```python


```

##### 7. En utilisant le package itertools et la fonction islice limitez le nombre de réseaux affichés à chaque print :
```python


```