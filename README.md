# Infrastructure L2/L3 - Segmentation & Redondance

## 1. Vue d'ensemble
Déploiement d'une architecture réseau hiérarchique. L'objectif est de valider la mise en place d'un cœur de réseau redondant et d'une segmentation stricte du trafic.
<img width="1756" height="648" alt="image" src="https://github.com/user-attachments/assets/5bfc457e-1868-47b4-833b-c3e91c476274" />


## 2. Implémentation technique

### Phase 1 : Configuration de base et Sécurité
Établissement d'une base de sécurité sur l'ensemble des switchs.
* Activation de **SSHv2**, chiffrement des mots de passe et gestion des accès console.
* 🔗 [Consulter le script de base](./configs/01_base_setup.txt)
## Phase 2 : Segmentation VLAN & Routage Inter-VLAN
Mise en place d'une isolation logique des services et centralisation du routage sur le cœur de réseau.

Segmentation multi-zones : Création des VLANs 10, 20, 30, 40 pour le siège et 70, 80 pour la partie opérationnelle.

Architecture de Routage Hybride :

Switch L3 (SVI) : Routage interne du siège pour garantir une commutation à vitesse filaire (Wire-speed).

Router-on-a-Stick : Utilisation de sous-interfaces sur le routeur central pour segmenter la zone opérationnelle.

Sécurité des Trunks : Modification du VLAN Natif (VLAN 99) sur les interconnexions pour prévenir les attaques de type VLAN Hopping.

Sécurisation des accès : Isolation de tous les ports inutilisés dans un VLAN 999 (BlackHole) avec extinction administrative.

🔗 Consulter le script de configuration VLAN
