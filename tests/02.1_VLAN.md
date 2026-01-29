# Validation Phase 2 : Segmentation VLAN & Routage

Ce document regroupe les tests techniques validant la segmentation logique du réseau et la communication inter-VLAN via le cœur de réseau (S3-L3).

---

## 1. État des VLANs et Affectation des Ports
L'objectif est de confirmer que chaque service est isolé dans son VLAN respectif et que les ports d'accès sont correctement configurés.

* **Commande exécutée :** `show vlan brief`
* **Points de contrôle :**  Présence de tous les VLANs (10, 20, 30, 40, 70, 80, 99, 999).
    * Statut `active` pour chaque VLAN.
    * Attribution correcte des ports `Fa0/X` aux VLANs correspondants.
    * Ports inutilisés affectés au VLAN 999 (UNUSED)

><img width="735" height="270" alt="image" src="https://github.com/user-attachments/assets/cccf6d9d-5a1d-4542-8aa5-c33f51535b03" />


---

## 2. Configuration des Liens Trunk (802.1Q)
Vérification que les liens inter-switchs transportent correctement les flux de tous les VLANs.

* **Commande exécutée :** `show interfaces trunk`
* **Points de contrôle :**  Mode de l'interface (`on`).
    * Encapsulation `802.1q`.
    * Native VLAN positionné sur le **VLAN 99**.

> <img width="635" height="181" alt="image" src="https://github.com/user-attachments/assets/cf79c75d-385a-4f44-9e01-a34d0fa3ed84" />


---

## 3. Interfaces de Routage (SVI) & Table de Routage
Preuve que le Switch L3 assure la fonction de passerelle par défaut pour l'ensemble du réseau.

* **Commandes exécutées :** `show ip interface brief` et `show ip route`
* **Points de contrôle :**  Statut `up/up` pour toutes les interfaces virtuelles (`Vlan 10`, `Vlan 20`, etc.).
    * Présence des réseaux connectés (`C`) dans la table de routage.

> <img width="635" height="329" alt="image" src="https://github.com/user-attachments/assets/cbc5ec63-ef1f-4061-b969-1478b0008019" />


---

## 4. Tests de Connectivité Inter-VLAN (Ping)
Validation finale de la communication entre différents hôtes

* **Action :** Test de connectivité depuis le terminal d'un PC vers un autre PC, exemple PC1 vers PC3.
* **Résultat attendu :** Réponse positive (Success rate is 100%).

> <img width="1325" height="229" alt="image" src="https://github.com/user-attachments/assets/f24fe164-8185-4100-bec7-dbc534b5c859" />

