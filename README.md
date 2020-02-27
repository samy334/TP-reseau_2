# TP 2
## I. Création et utilisation simples d'une VM CentOS
### 4. Configuration réseau d'une machine CentOS

Commande utilisée sur la VM : ip a

| name   | IP        | MAC               | Fonction                   |
|--------|-----------|-------------------|----------------------------|
| lo     | 127.0.0.1 | 00:00:00:00:00:00 | loopback                   |
| enp0s3 | 10.0.2.15 | 08:00:27:db:61:76 | Carte NAT (accès internet) |
| enp0s8 | 10.2.1.4  | 08:00:27:86:f1:fb | Carte NAT (accès internet) |

Adresse IP actuelle : 10.2.1.4
```bash
[root@localhost ~]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:db:61:76 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global noprefixroute dynamic enp0s3
        valid_lft 86101sec preferred_lft 86101sec
    inet6 fe80::f39d:8127:1f12:abfd/64 scope link noprefixroute
        valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:86:f1:fb brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.4/24 brd 10.2.1.255 scope global noprefixroute enp0s8
        valid_lft forever preferred_lft forever
    inet6 fe80::c87c:9447:9db2:38f9/64 scope link noprefixroute
        valid_lft forever preferred_lft forever
```
                                                                                                                                        

Pour changer l'IP, on se rend dans `cd /etc/sysconfig/network-scripts`
Pour changer l'IP de la carte réseau hôte, on va modifier via vim le fichier `ifcfg-enp0s8` et changer l'adresse à la ligne `IPADDR="10.2.1.4"`. On va la changer en `IPADDR="10.2.1.5"`.

On fait un  `service network restart` pour redémarrer le service réseau et boum l'IP est changée
```bash
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 08:00:27:86:f1:fb brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.5/24 brd 10.2.1.255 scope global noprefixroute enp0s8
        valid_lft forever preferred_lft forever
    inet6 fe80::c87c:9447:9db2:38f9/64 scope link noprefixroute
        valid_lft forever preferred_lft forever
    ```
    On test un ping
    ```powershell
PS C:\Windows\system32> ping 10.2.1.5

Envoi d’une requête 'Ping'  10.2.1.5 avec 32 octets de données :
Réponse de 10.2.1.5 : octets=32 temps<1ms TTL=64
Réponse de 10.2.1.5 : octets=32 temps=1 ms TTL=64
Réponse de 10.2.1.5 : octets=32 temps<1ms TTL=64
Réponse de 10.2.1.5 : octets=32 temps<1ms TTL=64

Statistiques Ping pour 10.2.1.5:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 1ms, Moyenne = 0ms
```
### 5. Appréhension de quelques commandes
    Lancement d'un scan 
    ```bash
    [root@localhost ~]#  nmap -A 10.2.1.5
    
Starting Nmap 6.40 ( http://nmap.org ) at 2020-02-13 15:40 CET
Nmap scan report for 10.2.1.5
Host is up (0.00015s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey: 2048 d6:21:5e:21:1e:ac:14:bd:0f:b7:2e:0a:1e:c8:8c:a9 (RSA)
|_256 8b:a3:1d:26:60:10:ab:e1:6e:9b:2e:bb:67:e7:66:43 (ECDSA)
Device type: general purpose
Running: Linux 3.X
OS CPE: cpe:/o:linux:linux_kernel:3
OS details: Linux 3.7 - 3.9
Network Distance: 0 hops

OS and Service detection performed. Please report any incorrect results at http://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 2.97 seconds
```
Scanner les ports TCP/UDP en écoute
```powershell
[root@localhost ~]# ss -ltunp
Netid  State      Recv-Q Send-Q                              
udp    UNCONN     0      0                                                *:68                                                           *:*                   users:(("dhclient",pid=3193,fd=6))
udp    UNCONN     0      0                                        127.0.0.1:323                                                          *:*                   users:(("chronyd",pid=795,fd=5))
udp    UNCONN     0      0                                            [::1]:323                                                       [::]:*                   users:(("chronyd",pid=795,fd=6))
tcp    LISTEN     0      100                                      127.0.0.1:25                                                           *:*                   users:(("master",pid=1540,fd=13))
tcp    LISTEN     0      128                                              *:22                                                           *:*                   users:(("sshd",pid=1215,fd=3))
tcp    LISTEN     0      100                                          [::1]:25                                                        [::]:*                   users:(("master",pid=1540,fd=14))
tcp    LISTEN     0      128                                           [::]:22                                                        [::]:*                   users:(("sshd",pid=1215,fd=4))
```
On peut vérifier quel programme envoie et lequel reçoit des données grâce au PID ou au nom inscrit juste en face.
Le programme `sshd` qui envoie des données doit par exemple être le daemon de la VM.

## II. Notion de ports
### 1. SSH
On peut vérifier sur quel port le serveur SSH écoute avec la commande utilisée précédemment. On peut voir que `sshd` écoute sur le port 22.

n peut vérifier la connexion ssh du port 22 avec la commande `ss -l -t`

```powershell
[root@localhost ~]# ss -l -t
State      Recv-Q Send-Q                                Local Address:Port                                                 Peer Address:Port
LISTEN     0      100                                       127.0.0.1:smtp                                                            *:*
LISTEN     0      128                                               *:ssh                                                             *:*
LISTEN     0      100                                           [::1]:smtp                                                         [::]:*
LISTEN     0      128                                            [::]:ssh                                                          [::]:*

```
On peut voir la ligne `ssh` présente.
Pour modifier le port du service SSH, il faut se rendre dans `/etc/ssh` et modifier le fichier `sshd_config`

A la ligne `# Port 22`, on enlève le # et on met le port souhaité, (j'ai mis 2222) on sauvegarde, on redémarre le service SSH via `systemctl` et on observe que ça marche pas. La firewall bloque le nouveau port du SSH, CentOS a un firewall de base. Du coup on va ouvrir le port correspondant du firewall.

Pour se faire, on va tout simplement taper la commande `sudo firewall-cmd --add-port=2222/tcp --permanent` pour ajouter le port 2222 aux ports autorisés par le firewall. Puis on recharge le tout avec la commande `sudo firewall-cmd --reload`.

```powershell
[root@localhost ~]# sudo firewall-cmd --list-all
public (active)
    target: default
    icmp-block-inversion: no
    interfaces: enp0s3 enp0s8
    sources:
    services: dhcpv6-client ssh
    ports: 2222/tcp
    protocols:
    masquerade: no
    forward-ports:
    source-ports:
    icmp-blocks:
    rich rules:
```

### B. Netcat

On fait ce qui est demandé de faire et on lance 2 powershell sur le PC.
On connecte l'un des powershell à la VM avec `nc -L -p 1025` et sur la VM, on exécute la commande `nc 192.168.0.20 1025` pour qu'ils puissent communiquer.
Sur le second powershell, on utilise netstat pour voir les connections entrantes 
```powershell
PS C:\Windows\System32\WindowsPowerShell\v1.0> netstat

Connexions actives

    Proto  Adresse locale         Adresse distante       État
    TCP    10.2.1.1:19022         10.2.1.5:ssh           ESTABLISHED
    [...]
    ```
