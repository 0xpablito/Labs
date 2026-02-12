# Validation Phase 3 : Haute Disponibilité & Performance Réseau

Ce document regroupe les tests de bon fonctionnement des liens agrégés et des sécurités de couche 2 appliquées

## Vérification technique - Liens et Sécurité (S1, S2, S3-L3)
L'objectif ici est de confirmer que les liens doublés sont bien actifs et que le réseau réagit correctement en cas de panne ou de branchement non autorisé.

## 1. État des liens EtherChannel
On s'assure que les interfaces physiques sont bien fusionnées en un seul canal logique via LACP.

* **Commande exécutée :** `show etherchannel summary`
* **Points de contrôle :**  Statut de l'agrégat : Doit être en (SU) (S = Layer 2, U = In Use).
    * Validation des membres : Chaque port physique doit avoir le flag (P).
    * Protocole : Confirmation du mode LACP (Standard).
 ><img width="636" height="272" alt="image" src="https://github.com/user-attachments/assets/62da4a04-7eac-4192-b492-fa9fe208d683" />

 ## 2. Validation des Trunks sur Port-Channel
Vérification que les interfaces virtuelles (Po1, Po2, Po3) transportent les VLANs sans erreur de configuration.

* **Commande exécutée :** `show interfaces trunk`
* **Points de contrôle :**  
    * Le Native VLAN doit être le 99 pour correspondre à la configuration de sécurité.
    * Le trunk doit désormais être porté par l'interface PoX (et non plus par les interfaces Fa0/X).
 ><img width="705" height="228" alt="image" src="https://github.com/user-attachments/assets/98d33ec5-8556-4adc-97ab-6da39f392655" />

  ## 3. Test de coupure
Validation réelle de la redondance en cas de défaillance d’un lien physique.

* **Action :** Lancement d'un ping continu vers la passerelle, puis suppression d'un des deux câbles d'un EtherChannel sous Packet Tracer.
* **Résultat attendu :**
   * Le flux ne doit pas s'arrêter.
   * Le Port-Channel reste opérationnel malgré la perte d'un lien physique.
   * Taux de réussite du ping : 100%.
><img width="1767" height="471" alt="image" src="https://github.com/user-attachments/assets/31008fa9-e2b4-47db-b7ab-1faaff498191" />


