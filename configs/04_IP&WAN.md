#  Configuration Phase 4 : Services IP & Connectivité WAN

Ce document détaille la mise en place du serveur DHCP, de la translation d'adresses (NAT) et des règles de sécurité (ACL) sur le Switch Cœur (S3-L3) et le Routeur-Central.


##  1.1 Adressage Dynamique - Zone Siège (S3-L3)
#### Automatisation de la configuration IP pour les services administratifs et commerciaux.

```
 --- Exclusions DHCP (Réservation pour passerelles et imprimantes) ---
ip dhcp excluded-address 192.168.10.1 192.168.10.5
ip dhcp excluded-address 192.168.20.1 192.168.20.5
ip dhcp excluded-address 192.168.30.1 192.168.30.5
ip dhcp excluded-address 192.168.40.1 192.168.40.5

 --- Création des Pools  ---
ip dhcp pool VLAN10_ADMIN
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8

ip dhcp pool VLAN20_PROD
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8

ip dhcp pool VLAN30_SALES
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8

ip dhcp pool VLAN40_GUEST
 network 192.168.40.0 255.255.255.0
 default-router 192.168.40.1
 dns-server 8.8.8.8
```
##  1.2 Adressage Dynamique - Zone Dépôt (Routeur-Central)
#### Gestion de l'adressage pour les partenaires et la logistique distante.
```
 --- Exclusions DHCP (Réservation pour passerelles et imprimantes) ---
ip dhcp excluded-address 172.16.70.1 172.16.70.5
ip dhcp excluded-address 172.16.80.1 172.16.80.5

 --- Création des Pools ---
ip dhcp pool VLAN70_PARTNERS
 network 172.16.70.0 255.255.255.0
 default-router 172.16.70.1
 dns-server 8.8.8.8

ip dhcp pool VLAN80_LOGISTICS
 network 172.16.80.1 255.255.255.0
 default-router 172.16.80.1
 dns-server 8.8.8.8
```
##  2. Sécurisation des accès et filtrage (ACL)
#### Restriction d'accès à l'imprimante réseau et protection des lignes VTY.
```
 --- ACL 101 : Isolation de l'imprimante (S3-L3) ---
access-list 101 deny ip 192.168.40.0 0.0.0.255 host 192.168.20.2 ! Bloque Guest vers Imprimante
access-list 101 permit ip any any                                ! Autorise le reste du trafic

interface Vlan 40
 ip access-group 101 in                                          ! Application du filtre sur le VLAN Guest

 --- ACL 10 : Sécurité Management (Tous équipements) ---
access-list 10 permit 192.168.10.0 0.0.0.255                     ! IP du VLAN Admin
line vty 0 15
 access-class 10 in                                              ! Seul le VLAN Admin peut configurer à distance
```
##  3. Connectivité Internet (Routeur)
#### Mise en place du NAT Overload (PAT) pour la sortie vers l'ISP.
```
---  Définition du trafic autorisé (ACL NAT) ---
Autorise le Siège (192.168.x.x) et le Dépôt (172.16.x.x)
access-list 1 permit 192.168.0.0 0.0.255.255
access-list 1 permit 172.16.0.0 0.0.255.255

---  Configuration du moteur NAT ---
ip nat inside source list 1 interface Serial0/1/0 overload

---  Assignation des rôles des interfaces ---
Interface vers le Siège
interface GigabitEthernet 0/0/0
 ip nat inside

Interfaces vers le Dépôt (Router-on-a-Stick)
Note : Le NAT doit être activé sur chaque sous-interface pour être effectif
interface GigabitEthernet 0/0/1.70
 ip nat inside
interface GigabitEthernet 0/0/1.80
 ip nat inside

Interface vers l'Internet (ISP)
interface Serial 0/1/0
 ip nat outside
```
##  4. Routage Statique
#### Configuration des chemins pour la communication inter-sites et l'accès extérieur.
```
 --- Routeur-Central ---
ip route 0.0.0.0 0.0.0.0 Serial0/1/0             ! Route par défaut (Internet)
ip route 192.168.0.0 255.255.0.0 10.0.0.2        ! Route de retour vers tous les VLANs du Siège

 --- Switch L3 ---
ip route 0.0.0.0 0.0.0.0 10.0.0.1                ! Le Switch L3 envoie tout au Routeur pour sortir
```
## 5. Simulation Internet (Routeur ISP)
#### Pour valider le NAT et la connectivité externe, une interface Loopback a été configurée sur le routeur ISP pour simuler le serveur DNS de Google.
```
interface Loopback0
 ip address 8.8.8.8 255.255.255.255
```
