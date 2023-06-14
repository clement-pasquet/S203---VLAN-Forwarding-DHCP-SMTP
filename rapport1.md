# Configuration réseau

* NOMS : Bernier Justine, Pasquet Clément
* GROUPE de TP : 1-2
* X : 181
* SECRET : arcturusepsilonscorpii
* IP_CLIENT : 172.20.181.1
* IP_DHCP : 172.20.181.2
* IP_SMTP : 192.168.0.81
* IPs du ROUTEUR : 172.20.181.3 / 192.168.0.81

**Note**: Le document suivant doit rendre compte de votre plan d’adressage (i.e. la description des différents LAN, de leur interconnexion, des machines avec les IP voire @MAC que vous jugerez pertinentes), de vos tables de routage de CLIENT, ROUTEUR, SMTP et celle (supposée) de DNS, des commandes à réaliser sur CLIENT, ROUTEUR, SMTP, et tout ce qui vous semble nécessaire à la configuration de votre réseau.

## But de la Partie 1
![](./diagramme1.png)

Il faut faire **cette** configuration, autrement dit, mettre un vlan et un lan ensemble via un pc appelé "routeur".
De plus, le DNS devra fournir l'adresse IP de "SMTP".

## Plan d'adressage


## Tables de routage

**Note**: il existe des générateurs de tables MDhttps://www.tablesgenerator.com/markdown_tables

| **destination** | **iface** | **gw** | 
|-----------------|-----------|--------|
|                 |           |        |
|                 |           |        |
|                 |           |        |


## Commandes de configuration

Commande base pour connaître sa configuration réseau :

```javascript
ip a 
```

La première chose à faire, est de reglé le fichier qui gère les interfaces pour éviter que les adresses IP "sautent" :

Il faut faire la commande suivante :

```sudo nano /etc/network/interfaces```

Et dans la page qui s'affichera, il faudra modifier les interfaces généralements nommés enosp1 et enosp2 par *Bleue et Jaune* comme dans l'exemple ci dessous :
```
auto lo
iface lo inet loopback

allow-hotplug eno1
iface eno1 inet manual
ip
allow-hotplug bleue
iface bleue inet manual

allow-hotplug jaune
iface jaune inet manual
```

Également, pour que le changement soit effective, il faut rédémarrer les systèmes en causes pour résoudre le problème :
```bash
systemctl restart networking
systemctl restart network-manager
```


## Étape 1 : Le Vlan

Pour pouvoir mettre en place le VLAN entre CLIENT, DHCP et ROUTEUR, il faut déjà vérifier que les interfaces en questions soit bien utilisables avec :
```bash
sudo ip link set jaune up
```
**Pour chaque PC**, cela permet de "mettre UP", c'est à dire d'activer, les interfaces jaune des différents PC.

<br></br>

Aussi, il est nécessaire dans le lancer **8021q**, un module du noyau Linux qui prend en charge les VLAN. 
Pour faire cela, nous utilisons la commande suivante :
```bash
modprobe 8021q
```

### Créer le Vlan :
Pour créer un Vlan, qui pourrait être grossièrement définie comme un sous réseau, il faut créer un *Vlan sur l'interface Jaune* .
Pour ce faire, il faut faire la commande suivante ( uniq. sur Linux ) :
```bash
    ip link add link jaune name jaune.181 type vlan id 181
```
Ps : il faut la faire pour tous les pc sur le vlan
**Ici**, le nom de ce Vlan sera `jaune.181`, et son id sera `181`, un chiffre spécial à notre groupe.

Ensuite, nous devons créer les ip's pour chaque machines dans le réseau :

<details>
<summary>IP du CLIENT ( cliquez pour dérouler )</summary>
ip a add 172.20.181.1/24 dev jaune.181
</details>
<details>
<summary>IP de DHCP ( cliquez pour dérouler )</summary>
ip a add 172.20.181.2/24 dev jaune.181 
</details>
<details>
<summary>IP de ROUTEUR ( cliquez pour dérouler )</summary>
ip a add 172.20.181.3/24 dev jaune.181
</details>

<br></br>

Aussi, le routeur possédant 2 ip, l'une dans le Vlan et l'autre dans le future lan, nous devons lui attribuer une ip pour le lan :
```bash
ip a add 192.168.0.181/24 dev jaune
```

Une fois que nous avons crées les 2 ip de ROUTEUR, il faut attribuer une adresse IP à SMTP, en effet, il est écrit qu'il faut que son adresse ip ( celle de SMTP ) soit donné grâce au DNS.
Pour faire cela, nous allons interroger le DNS.
Pour rappel, le rôle d'un DNS est de "relier" les liens internet ( google.com ... smtp181.mail181.com ... ) et les adresses ip des serveurs d'Internet.

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



