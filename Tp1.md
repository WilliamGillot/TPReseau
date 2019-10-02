# TP1 : Back to basics

# I. Gather informations

* 🌞 récupérer une **liste des cartes réseau** avec leur nom, leur IP et leur adresse MAC
```bash
[centos8@localhost /]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:53:5f:73 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86025sec preferred_lft 86025sec
    inet6 fe80::4ad6:a897:e477:132f/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:3e:44:b1 brd ff:ff:ff:ff:ff:ff
    inet 192.168.56.102/24 brd 192.168.56.255 scope global dynamic noprefixroute enp0s8
       valid_lft 825sec preferred_lft 825sec
    inet6 fe80::f4b1:8c2a:e48d:b55c/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

* 🌞 déterminer si les cartes réseaux ont récupéré une **IP en DHCP** ou non
  * si oui, affichez le bail DHCP utilisé par la machine
    * hint : tous les baux DHCP que votre machine stocke sont dans `/var/lib/NetworkManager`
    * hint2 : vous pouvez récupérer des infos sur le bail actuel d'une interface donnée avec :
      *  `sudo nmcli con show <INTERFACE_NAME>` pour toutes les infos
      *  `sudo nmcli -f DHCP4 con show <INTERFACE_NAME>` pour les infos DHCP uniquement
```bash
[centos8@localhost NetworkManager]$ sudo nmcli con show Wired\ connection\ 1
IP4.ADDRESS[1]:                         192.168.56.102/24
IP4.GATEWAY:                            --
IP4.ROUTE[1]:                           dst = 192.168.56.0/24, nh = 0.0.0.0, mt>
DHCP4.OPTION[1]:                        expiry = 1569506953
DHCP4.OPTION[2]:                        ip_address = 192.168.56.102
DHCP4.OPTION[3]:                        requested_broadcast_address = 1
DHCP4.OPTION[4]:                        requested_dhcp_server_identifier = 1
DHCP4.OPTION[5]:                        requested_domain_name = 1
DHCP4.OPTION[6]:                        requested_domain_name_servers = 1
DHCP4.OPTION[7]:                        requested_domain_search = 1
DHCP4.OPTION[8]:                        requested_host_name = 1
DHCP4.OPTION[9]:                        requested_interface_mtu = 1
DHCP4.OPTION[10]:                       requested_ms_classless_static_routes = 1
DHCP4.OPTION[11]:                       requested_nis_domain = 1
DHCP4.OPTION[12]:                       requested_nis_servers = 1
DHCP4.OPTION[13]:                       requested_ntp_servers = 1
DHCP4.OPTION[14]:                       requested_rfc3442_classless_static_rout>
DHCP4.OPTION[15]:                       requested_routers = 1
DHCP4.OPTION[16]:                       requested_static_routes = 1
DHCP4.OPTION[17]:                       requested_subnet_mask = 1
DHCP4.OPTION[18]:                       requested_time_offset = 1
DHCP4.OPTION[19]:                       requested_wpad = 1
DHCP4.OPTION[20]:                       subnet_mask = 255.255.255.0
IP6.ADDRESS[1]:                         fe80::f4b1:8c2a:e48d:b55c/64
IP6.GATEWAY:                            --
IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 101
IP6.ROUTE[2]:                           dst = ff00::/8, nh = ::, mt = 256, tabl>

```
* 🌞 afficher la **table de routage** de la machine et sa **table ARP**
  * expliquez chacune des lignes des deux tables 
  * *"cette route est vers le réseau XXX (nom + adresse réseau), elle est utilisée pour une connexion (locale|externe), la passerelle de cette route est à l'IP XXX et cette IP est portée par XXX"* par exemple

**table de routage** 
```bash
[centos8@localhost ~]$ ip route
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
192.168.56.0/24 dev enp0s8 proto kernel scope link src 192.168.56.102 metric 101

Cette route est vers le réseau wan 10.0.2.2.
10.0.2.0/24 indique que le réseau est local, elle est utilisée pour une connexion locale.
La passerelle de cette route est à l IP 10.0.2.15 et cette IP est portée par enp0s3.
192.168.56.0/24 indique que le réseau est local, elle est utilisée pour une connexion locale.
La passerelle de cette route est à l IP 192.168.56.102 et cette IP est portée par enp0s8.
```
**table ARP**
```bash
[centos8@localhost ~]$ arp -a
? (10.0.2.2) at 52:54:00:12:35:02 [ether] on enp0s3
? (192.168.56.100) at 08:00:27:5a:86:31 [ether] on enp0s8

10.0.2.2 est la passerelle du réseau 10.0.2.0/24  et 52:54:00:12:35:02 comme adresse mac portée par enp0s3.
192.168.56.100 est l adresse ip de l ordinateur sur le réseau et 08:00:27:5a:86:31 comme adresse mac portée par enp0s8.
```

* 🌞 récupérer **la liste des ports en écoute** (*listening*) sur la machine (TCP et UDP)
```bash
[centos8@localhost ~]$ netstat -atu
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:ssh             0.0.0.0:*               LISTEN
tcp        0     64 localhost.localdoma:ssh 192.168.56.1:49992      ESTABLISHED
tcp6       0      0 [::]:ssh                [::]:*                  LISTEN
udp        0      0 localhost.locald:bootpc 0.0.0.0:*
udp        0      0 localhost.locald:bootpc 0.0.0.0:*
udp        0      0 localhost:323           0.0.0.0:*
udp6       0      0 localhost:323           [::]:*
```
  * trouver/déduire la liste des applications qui écoutent sur chacun des ports TCP ou UDP repérés comme étant en écoute sur la machine (au moins un serveur SSH)
```bash
Le SSh écoute sur le port 22 de la machine.
```
* 🌞 récupérer **la liste des DNS utilisés par la machine**
```bash
[centos8@localhost ~]$ command nmcli device show enp0s3
GENERAL.DEVICE:                         enp0s3
GENERAL.TYPE:                           ethernet
GENERAL.HWADDR:                         08:00:27:53:5F:73
GENERAL.MTU:                            1500
GENERAL.STATE:                          100 (connected)
GENERAL.CONNECTION:                     enp0s3
GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/ActiveConnection/1
WIRED-PROPERTIES.CARRIER:               on
IP4.ADDRESS[1]:                         10.0.2.15/24
IP4.GATEWAY:                            10.0.2.2
IP4.ROUTE[1]:                           dst = 0.0.0.0/0, nh = 10.0.2.2, mt = 100
IP4.ROUTE[2]:                           dst = 10.0.2.0/24, nh = 0.0.0.0, mt = 100
IP4.DNS[1]:                             10.33.10.20
IP4.DNS[2]:                             10.33.10.2
IP4.DNS[3]:                             8.8.8.8
IP4.DNS[4]:                             8.8.4.4
IP4.DOMAIN[1]:                          auvence.co
IP6.ADDRESS[1]:                         fe80::4ad6:a897:e477:132f/64
IP6.GATEWAY:                            --
IP6.ROUTE[1]:                           dst = fe80::/64, nh = ::, mt = 100
IP6.ROUTE[2]:                           dst = ff00::/8, nh = ::, mt = 256, table=255
```
  * effectuez une requête DNS afin de récupérer l'adresse IP associée au domaine `www.reddit.com` ~~(parce que c'est important d'avoir les bonnes adresses)~~
 ```bash
 [centos8@localhost ~]$ ping www.reddit.com
PING reddit.map.fastly.net (151.101.129.140) 56(84) bytes of data.
```

* 🌞 afficher **l'état actuel du firewall**
```bash
[centos8@localhost ~]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client ssh
  ports:
  protocols:
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
```
  * quelles interfaces sont filtrées ?
  ```bash
interfaces: enp0s3 enp0s8
```
  * quel port TCP/UDP sont autorisés/filtrés ?
  ```bash
  services: cockpit dhcpv6-client ssh
  ```
  * 🐙 sous CentOS8, ce n'est plus `iptables` qui est utilisé pour manipuler le filtrage réseau mais `nftables`. Jouez un peu avec `nft` et affichez les "vraies" règles firewall (`firewalld`, manipulé avec `firewall-cmd` n'est qu'une surcouche à `nft`)

## II. Edit configuration

**Deuxièmement : Modifier la configuration existante**

Commandes à utiliser (ou pas) : `vim`, `cat`, `nmcli`, `systemctl`, `firewall-cmd`

---

### 1. Configuration cartes réseau

**NB** : sur CentOS8, la gestion des cartes réseau a légèrement changé. Il existe un démon qui gère désormais tout ce qui est relatif au réseau : NetworkManager.  

Marche à suivre pour modifier la configuration d'une carte réseau :
* édition du fichier de configuration
  * `sudo vim /etc/sysconfig/network-scripts/ifcfg-enp0s8`
* refresh de NetworkManager ("Hey prend mes modifications en compte stp !")
  * `sudo nmcli connection reload` 
  * `sudo nmcli con reload` même chose, on peut abréger les commandes `nmcli`
  * `sudo nmcli c reload` même chose aussi
* restart de l'interface
  * `sudo ifdown enp0s8` puis `sudo ifup enp0s8`
  * **OU** `sudo nmcli con up enp0s8`

> Pour les hipsters, y'a moyen de ne plus passer du tout par les fichiers dans `/etc/sysconfig` et tout gérer directement avec NetworkManager, cf [la doc officielle](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/configuring_and_managing_networking/index#Selecting-Network-Configuration-methods_overview-of-Network-configuration-methods). 

---

* 🌞 modifier la configuration de la carte réseau privée
  * modifier la configuration de la carte réseau privée pour avoir une nouvelle IP statique définie par vos soins
```bash
[centos8@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s8
DEVICE=enp0s8
BOOTPROTO=none
ONBOOT=yes
NETMASK=255.255.255.0
IPADDR=192.168.0.2
```
* ajouter une nouvelle carte réseau dans un DEUXIEME réseau privé UNIQUEMENT privé
  * il faudra par exemple créer un nouveau host-only dans VirtualBox
  * 🌞 dans la VM définir une IP statique pour cette nouvelle carte
```bash
[centos8@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s2
DEVICE=enp0s2
BOOTPROTO=none
ONBOOT=yes
NETMASK=255.255.255.0
IPADDR=192.168.0.4
```
* vérifier vos changements
  * afficher les nouvelles cartes/IP
  * vérifier les nouvelles tables ARP/de routage
* 🐙 mettre en place un NIC *teaming* (ou *bonding*)
  * il vous faut deux cartes dans le même réseau puisque vous allez les agréger (vous pouvez en créer de nouvelles)
  * le *teaming* ou *bonding* consiste à agréger deux cartes réseau pour augmenter les performances/la bande passante
  * je vous laisse free sur la configuration (active/passive, loadbalancing, round-robin, autres)
  * prouver que le NIC *teaming* est en place

---

### 2. Serveur SSH

* 🌞 modifier la configuration du système pour que le serveur SSH tourne sur le port 2222
```bash
[centos8@localhost ~]$ sudo nano /etc/ssh/sshd_config
...
#Port 2222
...
```
  * adapter la configuration du firewall (fermer l'ancien port, ouvrir le nouveau)
```bash
sudo semanage port -a -t ssh_port_t -p tcp 2222
```
* pour l'étape suivante, il faudra un hôte qui ne s'est jamais connecté à la VM afin d'observer les échanges ARP (vous pouvez aussi juste vider la table ARP du client). Je vous conseille de faire une deuxième VM dans le même réseau, mais vous pouvez utiliser votre PC hôte.
* 🌞 analyser les trames de connexion au serveur SSH
  * intercepter avec Wireshark et/ou `tcpdump` le trafic entre le client SSH et le serveur SSH
  * détailler l'établissement de la connexion
    * doivent figurer au moins : échanges ARP, 3-way handshake TCP
    * 🐙 configurer une connexion par échange de clés, analyser les échanges réseau réalisés par le protocole SSH au moment de la connexion
  * une fois la connexion établie, choisir une trame du trafic SSH et détailler son contenu

# III. Routage simple

Dans cette partie, vous allez remettre en place un routage statique simple. Vous êtes libres du choix de la techno (CentOS8, Cisco, autres. Vous pouvez utiliser GNS3). 

Vous devez reproduire la mini-archi suivante : 
```
                   +-------+
                   |Outside|
                   | world |
                   +---+---+
                       |
                       |
+-------+         +----+---+         +-------+
|       |   net1  |        |   net2  |       |
|  VM1  +---------+ Router +---------+  VM2  |
|       |         |        |         |       |
+-------+         +--------+         +-------+
```

* **Description**
  * Le routeur a trois interfaces, dont une qui permet de joindre l'extérieur (internet)
  * La `VM1` a une interface dans le réseau `net1`
  * La `VM2` a une interface dans le réseau `net2`
  * Les deux VMs peuvent joindre Internet en passant par le `Router`
* 🌞 **To Do** 
  * Tableau récapitulatif des IPs
  * Configuration (bref) de VM1 et VM2
  * Configuration routeur
  * Preuve que VM1 passe par le routeur pour joindre internet
  * Une (ou deux ? ;) ) capture(s) réseau ainsi que des explications qui mettent en évidence le routage effectué par le routeur

# IV. Autres applications et métrologie

Dans cette partie, on va jouer un peu avec de nouvelles commandes qui peuvent être utiles pour diagnostiquer un peu ce qu'il se passe niveau réseau.

---

## 1. Commandes

* jouer avec `iftop`
  * expliquer son utilisation et imaginer un cas où `iftop` peut être utile
```bash
Ecouter une interface spécifique, voir le traffic.
Avec un sudo iftop -F net/mask on peut voir le trafic d entrée et de sortie sur un réseau IPV4.
```
---
## 2. Cockpit

* 🌞 mettre en place cockpit sur la VM1
  * c'est quoi ? C'est un service web. Pour quoi faire ? Vous allez vite comprendre en le voyant.
  * `sudo dnf install -y cockpit`
  * `sudo systemctl start cockpit`
  * trouver (à l'aide d'une commande shell) sur quel port (TCP ou UDP) écoute Cockpit 
```bash
[centos8@localhost ~]$ info cockpit
Il écoute donc sur le port TCP 9090
```
  * vérifier que le port est ouvert dans le firewall
* 🌞 explorer Cockpit, plus spécifiquement ce qui est en rapport avec le réseau
---

## 3. Netdata

Netdata est un outil utilisé pour récolter des métriques et envoyer des alertes. Il peut aussi être utilisé afin de visionner ces métriques, à court terme. Nous allons ici l'utiliser pour observer les métriques réseau et mettre en place un service web supplémentaire.

* 🌞 mettre en place Netdata sur la VM1 et la VM2
  * se référer à la documentation officielle
  * repérer et ouvrir le port dédié à l'interface web de Netdata
* 🌞 explorer les métriques liées au réseau que récolte Netdata
