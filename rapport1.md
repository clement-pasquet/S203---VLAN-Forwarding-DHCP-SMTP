# Configuration réseau

* NOMS : Bernier Justine, Pasquet Clément
* GROUPE de TP : 1-2
* X : 181
* SECRET : arcturusepsilonscorpii
* IP_CLIENT : 
* IP_DHCP : 
* IP_SMTP : 
* IPs du ROUTEUR : 

**Note**: Le document suivant doit rendre compte de votre plan d’adressage (i.e. la description des différents LAN, de leur interconnexion, des machines avec les IP voire @MAC que vous jugerez pertinentes), de vos tables de routage de CLIENT, ROUTEUR, SMTP et celle (supposée) de DNS, des commandes à réaliser sur CLIENT, ROUTEUR, SMTP, et tout ce qui vous semble nécessaire à la configuration de votre réseau.

## Plan d'adressage


## Tables de routage

**Note**: il existe des générateurs de tables MDhttps://www.tablesgenerator.com/markdown_tables

| **destination** | **iface** | **gw** |
|-----------------|-----------|--------|
|                 |           |        |
|                 |           |        |
|                 |           |        |


## Commandes de configuration

Connaître sa configuration réseau 

    ip a 

1er étape trouver que les interfaces sont à UP.
manip pour pas que cela saute:
sudo nano /etc/network/interfaces
'''
auto lo
iface lo inet loopback

allow-hotplug eno1
iface eno1 inet manual
ip
allow-hotplug bleue
iface bleue inet manual

allow-hotplug jaune
iface jaune inet manual
'''

systemctl restart networking
systemctl restart network-manager
Pour redemarrer le système.

pour mettre en place le VLAN 181 entre client, dhcp et routeur
la commande ci dessous sers à pouvoir utiliser l'interface jaune.
sudo ip link set jaune up

modprobe 8021q
sers a lancer le service a propos du service internet 8021q.

ip link add link jaune name jaune.181 type vlan id 181
sers a créer un sous-réseau d'une interface.

Nous avons créer les addresses ip de chaque machines le client qui finis par .1,  addresse ip du DHCP.
ip client
ip a add 172.20.181.1/24 dev jaune.181

ip dhcp
ip a add 172.20.181.2/24 dev jaune.181

ip routeur
ip a add 172.20.181.3/24 dev jaune.181

Début Lan routeur
ip a addr 192.168.0.181

commande pour interroger le dns à partir du routeur afin d'avoir l'@ ip de smtp :
nslookup smtp181.mail181.com addressIpDNS
nslookup smtp181.mail181.com 192.168.0.254

lan smtp
ip a addr 192.168.0.AddrIp
ip a addr 192.168.0.81/24 dev jaune

Nous avons ensuite testé en mettant la vlan à UP
ip link set jaune.181
dhcp a vérifié saconnection avec le routeur
ping 172.20.181.3

> forwarding du routeur
nano /etc/sysctl.conf
nous avons décommenté #net.ipv4.ip_forward=1
ensuite il faut redémarrer le service réseau:
sysctl -p

nous avons ajouter la route pour le client et dhcp et le routeur
ip r add 192.168.0.0/24 via 172.20.181.3

nous avons ajouter ensuite la route pour smtp
ip r add 172.20.181.0/24 via 192.168.0.181

Nous avons tester avec un ping
ping 172.20.181.2 côté smtp
et ping 192.168.0.81 côté client
et ça marche !!!!

Log commande
Il faut créer des log. Pour cela nous avons téléchargé un fichier log donné par nos log.
Cependant, en premier il faut se donner le droit d'accéder au fichier avec 
`chmod 777 log`.
Ensuite, nous avons executé ce fichier que nous avons écrit dans le fichier CLIENT.log avec la commande :
`./log > CLIENT.log` ( pour le client ).

plan d'addressage
Client 

#analyse dns.zjfo
nous avons via la machine client réalisé la commande nslookup smtp181.mail181.com 192.168.0.254



