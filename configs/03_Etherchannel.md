# Configuration EtherChannel LACP (IEEE 802.3ad)
**Cible :** `S1`, `S2`, `S3-L3`


### 📋 Script de configuration

### Configuration sur S1 (Vers S3 et S2)
```
 --- Groupe 1 : Liaison vers S3-L3 ---
interface range f0/21 - 22
 shutdown ! On coupe les ports pour éviter les erreurs STP pendant la config
 channel-group 1 mode active ! Active = protocole LACP (Standard)
 switchport mode trunk ! Force le lien en Trunk pour passer tous les VLANs
 no shutdown ! On relance proprement l'agrégation

 --- Groupe 3 : Liaison vers S2 ---
interface range f0/23 - 24
 shutdown
 channel-group 3 mode active
 switchport mode trunk
 no shutdown
```
### Configuration sur S2 (Vers S3 et S1)
```
 --- Groupe 2 : Liaison vers S3-L3 ---
interface range f0/23 - 24
 shutdown
 channel-group 2 mode active
 switchport mode trunk
 no shutdown

 --- Groupe 3 : Liaison vers S1 ---
interface range f0/1 - 2
 shutdown
 channel-group 3 mode active
 switchport mode trunk
 no shutdown
```
### Configuration sur S3-L3 
```
 --- Groupe 1 : Liaison vers S1 ---
interface range f0/1 - 2
 shutdown
 channel-group 1 mode active   ! Agrégation côté Cœur pour S1
 switchport mode trunk
 no shutdown

 --- Groupe 2 : Liaison vers S2 ---
interface range f0/3 - 4
 shutdown
 channel-group 2 mode active   ! Agrégation côté Cœur pour S2
 switchport mode trunk
 no shutdown
```
### Sécurisation des ports d'accès (STP Hardening)
Exemple pour S1
```
 --- Configuration des ports utilisateurs  ---
interface range f0/1 - 10     ! On cible les ports qui ne sont pas des Trunks
 spanning-tree portfast       ! Active la connectivité immédiate (évite les 30s d'attente)
 spanning-tree bpduguard enable ! Sécurité : coupe le port si on y branche un switch pirate
interface range f0/1 - 5
 description LAN10
interface range f0/6-10
 description LAN20
```   
