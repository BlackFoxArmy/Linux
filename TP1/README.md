# TP1 : (re)Familiaration avec un système GNU/Linux

## 0. Préparation de la machine

**🌞 Setup de deux machines Rocky Linux configurées de façon basique.**


* un accès internet (via la carte NAT)

* carte réseau dédiée
* route par défaut
```
VM 1 : 
[maxime@node1 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:4f:97:53 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86269sec preferred_lft 86269sec
    inet6 fe80::a00:27ff:fe4f:9753/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e7:37:de brd ff:ff:ff:ff:ff:ff
    inet 10.101.1.11/24 brd 10.101.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fee7:37de/64 scope link
       valid_lft forever preferred_lft forever
```
```
VM 2: 
[maxime@node2 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:4f:97:53 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86250sec preferred_lft 86250sec
    inet6 fe80::a00:27ff:fe4f:9753/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:18:b6:00 brd ff:ff:ff:ff:ff:ff
    inet 10.101.1.12/24 brd 10.101.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe18:b600/64 scope link
       valid_lft forever preferred_lft forever
```
* un accès à un réseau local (les deux machines peuvent se ping) (via la carte Host-Only)
VM 1: 10.2.1.10
```
[maxime@node1 ~]$ ping 10.101.1.12 pôur ping node 2
PING 10.101.1.12 (10.101.1.12) 56(84) bytes of data.
64 bytes from 10.101.1.12: icmp_seq=1 ttl=64 time=0.496 ms
64 bytes from 10.101.1.12: icmp_seq=2 ttl=64 time=0.199 ms
```
```
VM 2:ping 10.101.1.11 pour ping node 1
[maxime@node2 ~]$ ping 10.101.1.11
PING 10.101.1.11 (10.101.1.11) 56(84) bytes of data.
64 bytes from 10.101.1.11: icmp_seq=1 ttl=64 time=0.392 ms
64 bytes from 10.101.1.11: icmp_seq=2 ttl=64 time=0.425 ms
```

* carte réseau dédiée (host-only sur VirtualBox)
les machines doivent posséder une IP statique sur l'interface host-only
```
L'IP statique de la node 1 est 10.101.1.11 
Et l'IP statique de la node 2 est 10.101.1.12
on peut confirmer mes dires avec la commande ip route show.

[maxime@node1 ~]$ ip route show
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
10.101.1.0/24 dev enp0s8 proto kernel scope link src 10.101.1.11 metric 101

[maxime@node2 ~]$ ip route show
default via 10.0.2.2 dev enp0s3 proto dhcp metric 100
10.0.2.0/24 dev enp0s3 proto kernel scope link src 10.0.2.15 metric 100
10.101.1.0/24 dev enp0s8 proto kernel scope link src 10.101.1.12 metric 101
[maxime@node2 ~]$
```

* vous n'utilisez QUE ssh pour administrer les machines
```
node 1 PS C:\WINDOWS\system32> ssh maxime@10.101.1.11

node 2: PS C:\WINDOWS\system32> ssh maxime2@10.101.1.12
```
* les machines doivent avoir un nom
```
Node 1 : [maxime@node1 ~]$ hostname
node1.tp1.b2

Node 2: [maxime@node2 ~]$ hostname
node2.tp1.b2
```

* utiliser 1.1.1.1 comme serveur DNS
```
Dans le fichier resolv.conf , il faut rentrer "nameserver 1.1.1.1"
```
* vérifier avec le bon fonctionnement avec la commande dig
```
Pour vérifier la commande "dig" , on fait "dig gitlab.com

[maxime@node1 ~]$ dig gitlab.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35580
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             47      IN      A       172.65.251.78

;; Query time: 24 msec
;; SERVER: 192.168.1.254#53(192.168.1.254)
;; WHEN: Thu Sep 23 10:36:46 CEST 2021
;; MSG SIZE  rcvd: 55
```
* avec dig, demander une résolution du nom ynov.com
```
La commande est : dig ynov.com
[maxime@node1 ~]$ dig gitlab.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 35580
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             47      IN      A       172.65.251.78

;; Query time: 24 msec
;; SERVER: 192.168.1.254#53(192.168.1.254)
;; WHEN: Thu Sep 23 10:36:46 CEST 2021
;; MSG SIZE  rcvd: 55

[maxime@node1 ~]$ dig ynov.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 45686
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               10800   IN      A       92.243.16.143

;; Query time: 19 msec
;; SERVER: 192.168.1.254#53(192.168.1.254)
;; WHEN: Thu Sep 23 10:37:11 CEST 2021
;; MSG SIZE  rcvd: 53

[maxime@node1 ~]$
```
* mettre en évidence la ligne qui contient la réponse : l'IP qui correspond au nom demandé
```
Voici la ligne  qui contient la réponse : ynov.com. 6882 IN  A  92.243.16.143
```
* mettre en évidence la ligne qui contient l'adresse IP du serveur qui vous a répondu

```
Voici la ligne  qui contient la réponse : ;; SERVER: 192.168.1.254#53(192.168.1.254)
```

* les machines doivent pouvoir se joindre par leurs noms respectifs

fichier /etc/hosts
assurez-vous du bon fonctionnement avec des ping <NOM>

```
Node 1 : 

[maxime@node1 ~]$ ping node2.tp1.b2
PING node2.tp1.b2 (10.101.1.12) 56(84) bytes of data.
64 bytes from node2.tp1.b2 (10.101.1.12): icmp_seq=1 ttl=64 time=0.192 ms
64 bytes from node2.tp1.b2 (10.101.1.12): icmp_seq=2 ttl=64 time=0.405 ms
```

```
Node 2
[maxime@node2 ~]$ ping node1.tp1.b2
PING node1.tp1.b2 (10.101.1.11) 56(84) bytes of data.
64 bytes from node1.tp1.b2 (10.101.1.11): icmp_seq=1 ttl=64 time=0.417 ms
64 bytes from node1.tp1.b2 (10.101.1.11): icmp_seq=2 ttl=64 time=0.368 ms
```

* le pare-feu est configuré pour bloquer toutes les connexions exceptées celles qui sont nécessaires
```
[maxime@node1 ~]$ ip n s
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
10.101.1.1 dev enp0s8 lladdr 0a:00:27:00:00:19 REACHABLE
10.101.1.12 dev enp0s8 lladdr 08:00:27:18:b6:00 STALE
[maxime@node1 ~]$

[maxime@node2 ~]$ ip n s
10.0.2.2 dev enp0s3 lladdr 52:54:00:12:35:02 STALE
10.101.1.11 dev enp0s8 lladdr 08:00:27:e7:37:de STALE
10.101.1.1 dev enp0s8 lladdr 0a:00:27:00:00:19 DELAY
[maxime@node2 ~]$
```

## I. Utilisateurs

* 1. Création et configuration

**🌞 Ajouter un utilisateur à la machine, qui sera dédié à son administration. Précisez des options sur la commande d'ajout pour que :** 

* le répertoire home de l'utilisateur soit précisé explicitement, et se trouve dans /home

* le shell de l'utilisateur soit /bin/bash

```
Voici la commande : sudo useradd -d /home/TP1_Linux  -s /bin/sh leo

elle permet d'avoir le répertoire home de l'utilisateur ( -d /home/TP1_Linux)
et (-s /bin/sh) permet l'utilisation du shell 
```

**🌞 Créer un nouveau groupe admins qui contiendra les utilisateurs de la machine ayant accès aux droits de root via la commande sudo.
Pour permettre à ce groupe d'accéder aux droits root :**

* il faut modifier le fichier /etc/sudoers

* on ne le modifie jamais directement à la main car en cas d'erreur de syntaxe, on pourrait bloquer notre accès aux droits administrateur

* la commande visudo permet d'éditer le fichier, avec un check de syntaxe avant fermeture

* ajouter une ligne basique qui permet au groupe d'avoir tous les droits (inspirez vous de la ligne avec le groupe wheel)
```
## Allows people in group admins to run all commands
%admins ALL=(ALL)       ALL
```
Grâce à cette ligne le groupe admins a les mêmes que le groupe wheel (ALL)

**🌞 Ajouter votre utilisateur à ce groupe admins.**

> Utilisateur créé et configuré
> Groupe admins créé
> Groupe admins ajouté au fichier /etc/sudoers

 Ajout de l'utilisateur au groupe admins : 
 ```
[maxime@node2 ~]$ sudo gpasswd -a leo admins
[sudo] password for maxime:
Adding user leo to group admins
```

 ## 2. SSH
 
 
*  🌞 Pour cela :

il faut générer une clé sur le poste client de l'administrateur qui se connectera à distance (vous :) )


déposer la clé dans le fichier /home/<USER>/.ssh/authorized_keys de la machine que l'on souhaite administrer
```
[maxime@node2 ~]$ cat .ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCeQHPd7PQh6Es/wNWQAgxf+1B78rJ9+g5N493q7SRsA1CEl7txmpDKLoESQtU+fUyfw4ZMMG7BUQfbVlfkPbXFRvKnw0IUvYGVwNjk7NLiYMg1jW5t1Tawdsak4/rGG86hX+bjZrYnOyht7OUpp3gkSVRdu4qY6m2ZNwXQpfK7Zn++STRckHnEuhoPKDj0PxaV7e5ZXn45XfTsly4rs4LuaiMWpneO94Q71HlTCle4Xr6y+mUHsLYyllDSN9sHF1pLLaiWsDaiTYPLQC3ff6J3fc3Jw6VSI6wRqjfZmnGIlQ7ZwBKf7HDwKxsbCLz/wSvxVm4gWxTrIGVKI4jAvwqt rsa-key-20210923
```
vous utiliserez l'utilisateur que vous avez créé dans la partie précédente du TP
on peut le faire à la main
ou avec la commande ssh-copy-id
```
[maxime@node2 ~]$ logout
Connection to 10.101.1.12 closed.
PS C:\WINDOWS\system32> ssh maxime@10.101.1.12
Activate the web console with: systemctl enable --now cockpit.socket

Last login: Thu Sep 23 14:16:56 2021 from 10.101.1.1
```
On peut voir que la connexion, s'est faites sans mot de passe .


## II. Partitionnement

### 1. Préparation de la VM

Ajout de deux disques dans la configuration de la vm (3Go chacun).

### 2. Partitionnement

#### 🌞 Utilisez LVM pour:

- Agréger les deux disques en un seul volume group:

```
[maxime@node1 ~]$ sudo pvcreate /dev/sdb
  Physical volume "/dev/sdb" successfully created.
[maxime@node1 ~]$ sudo pvcreate /dev/sdc
  Physical volume "/dev/sdc" successfully created.
[maxime@node1 ~]$ sudo vgcreate data /dev/sdb /dev/sdc
  Volume group "data" successfully created
```

- Créer 3 logical volumes de 1 Go chacun

```
[maxime@node1 ~]$ sudo lvcreate -L 1G data -n ma_data_1
  Logical volume "ma_data_1" created.
[maxime@node1 ~]$ sudo lvcreate -L 1G data -n ma_data_2
  Logical volume "ma_data_2" created.
[maxime@node1 ~]$ sudo lvcreate -L 1G data -n ma_data_3
  Logical volume "ma_data_3" created.
```

- Formater ces partitions en ext4

```
[maxime@node1 ~]$ sudo mkfs -t ext4 /dev/data/ma_data_1
mke2fs 1.45.6 (20-Mar-2020)
Creating filesystem with 262144 4k blocks and 65536 inodes
Filesystem UUID: 78987328-4d03-44e0-af95-018a20616fdb
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal (8192 blocks): done
Writing superblocks and filesystem accounting information: done
```

Je fais pareil pour les partitions 2 et 3

- Monter ces partitions pour qu'elles soient accessibles aux points de montage /mnt/part1, /mnt/part2 et /mnt/part3.

```
[maxime@node1 ~]$ sudo mount /dev/data/ma_data_1 /mnt/part1
[maxime@node1 ~]$ sudo mount /dev/data/ma_data_2 /mnt/part2
[maxime@node1 ~]$ sudo mount /dev/data/ma_data_3 /mnt/part3
```

#### 🌞 Grâce au fichier /etc/fstab, faites en sorte que cette partition soit montée automatiquement au démarrage du système.

On ajoute la ligne: /dev/data/lv_data1 /mnt/part1 ext4 defaults 0 0 dans le fichier /etc/fstab pour que les partitions soient montées automatiquement.

## III. Gestion de services

### 1. Interaction avec un service existant

#### 🌞 Assurez-vous que:

```
[maxime@node1 ~]$ systemctl is-enabled firewalld
enabled
```

```
[maxime@node1 ~]$ systemctl is-active firewalld
active
```

L'unité est démarrée et est activée

### 2. Création de service

#### A. Unité simpliste

#### 🌞 Créer un fichier qui définit une unité de service web.service dans le répertoire /etc/systemd/system

```
[maxime@node1 ~]$ sudo nano /etc/systemd/system/web.service
```

#### 🌞Une fois le service démarré, assurez-vous que pouvez accéder au serveur web : avec un navigateur ou la commande curl sur l'IP de la VM, port 8888.

```
[maxime@node1 ~]$ curl 10.101.1.11:8888
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=utf-8">
<title>Directory listing for /</title>
</head>
<body>
<h1>Directory listing for /</h1>
<hr>
<ul>
<li><a href="bin/">bin@</a></li>
<li><a href="boot/">boot/</a></li>
<li><a href="dev/">dev/</a></li>
<li><a href="etc/">etc/</a></li>
<li><a href="home/">home/</a></li>
<li><a href="lib/">lib@</a></li>
<li><a href="lib64/">lib64@</a></li>
<li><a href="media/">media/</a></li>
<li><a href="mnt/">mnt/</a></li>
<li><a href="opt/">opt/</a></li>
<li><a href="proc/">proc/</a></li>
<li><a href="root/">root/</a></li>
<li><a href="run/">run/</a></li>
<li><a href="sbin/">sbin@</a></li>
<li><a href="srv/">srv/</a></li>
<li><a href="sys/">sys/</a></li>
<li><a href="tmp/">tmp/</a></li>
<li><a href="usr/">usr/</a></li>
<li><a href="var/">var/</a></li>
</ul>
<hr>
</body>
</html>
```

### B. Modification de l'unité

#### 🌞 Créer un utilisateur web.

```
[maxime@node1 ~]$ sudo useradd web
```

#### 🌞 Modifiez l'unité de service web.service créée précédemment en ajoutant les clauses:

```
[...]
[Service]
ExecStart=/bin/python3 -m http.server 8888
User=web
WorkingDirectory= /srv/web
[...]
```

🌞 Placer un fichier de votre choix dans le dossier créé dans /srv et tester que vous pouvez y accéder une fois le service actif. Il faudra que le dossier et le fichier qu'il contient appartiennent à l'utilisateur web.

J'ai créé un fichier test dans /srv/web:

```
[maxime@node1 ~]$ sudo ls /srv/web
test
```

Je fais un chmod 700 sur le dossier web et je lance le serveur avec l'utilisateur web pour voir si il fonctionne:

```
[web@node1 maxime]$ sudo systemctl status web --no-pager
● web.service - Very simple web service
   Loaded: loaded (/etc/systemd/system/web.service; enabled; vendor preset: disabled)
   Active: active (running) since Sun 2021-09-26 17:26:11 CEST; 26min ago
 Main PID: 2250 (python3)
    Tasks: 1 (limit: 11397)
   Memory: 10.3M
   CGroup: /system.slice/web.service
           └─2250 /bin/python3 -m http.server 8888

Sep 26 17:26:11 node1.tp1.b2 systemd[1]: Started Very simple web …ce.
Hint: Some lines were ellipsized, use -l to show in full.
```