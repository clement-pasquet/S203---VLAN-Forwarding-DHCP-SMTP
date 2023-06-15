# Manuel utilisateur

* NOMS : 
* GROUPE de TP :  
* X : 
* SECRET : 
* IP_CLIENT : 
* IP_DHCP : 
* IP_SMTP : 
* IPs du ROUTEUR : 


**Note**: Ce document d’une à deux pages, doit décrire au choix : 
* soit comment comment installer un VLAN 
* soit comment configurer un serveur DHCP. 
Une personne ne sachant pas configurer un de ces services doit être capable de le faire en suivant le manuel (description globales des étapes et détails sur les différentes machines, commandes à exécuter, codes à modifier, commandes de test à réaliser, résultats à observer, etc.). Votre manuel doit être complet e.g. si votre service requiert une configuration réseau alors votre manuel doit aussi l'expliquer. Indiquer explicitement les adresses IP et éventuellement les adresses MAC des machines que vous utilisez si c’est pertinent.

Serveur DHCP
Premièrement il faut parametrer notre serveur dhcp, comme dans le tp 1.

Premièrement, il modifier le fichier `/etc/default/isc-dhcp-server`
pour, dans la ligne 
```bash
INTERFACESv4=""
```
mettre le vlan que nous avons crée précédemment :
```bash
INTERFACESv4="jaune.181"
```
ensuite, dans
`/etc/dhcp/dhcpd.conf`, qui est le fichier de paramétrage du DHCP, il faut mettre les lignes suivantes :
```bash
subnet 172.20.181.0 netmask 255.255.255.0 {
	range 172.20.181.2 172.20.181.253;
	option domain-name-serveurs 192.168.0.254;
	option domain-name "ARCTURUSEPSILONCORPII.org";
	option routers 172.20.181.254;
	host CLIENT {
		hardware ethernet 04:8d:38:c2:57:e6;
		fixed-address 172.20.181.1;
	}
}
```

Sur  le pc client, avant de faire les commandes du serveur dhcp, il faut supprimrer l'addresse ip du vlan du client : 
```bash
ip a del 172.20.181.1/24 dev jaune.181
```
Ensuite, il faut demander au serveur dhcp de nous fournir ( entre autre ) une ip avec la commande suivante :
```bash
dhclient jaune.181
```
ATTENTION, si rien n'est fournie à la commande DHCLIENT, la commande peut CASSER LES PC DE LA SALLE RÉSEAU, à faire attention donc.
