# Validation Phase 4 : Services IP & Connectivité WAN

Ce document regroupe les tests techniques validant l'automatisation de l'adressage (DHCP), la sécurité périmétrique (ACL), la translation d'adresses (NAT) et le routage de bout en bout.

## 📍 Services Réseau & Accès Distant (Global)
#### Cette partie valide la configuration des services IP et la capacité du réseau à communiquer avec l'extérieur tout en respectant les politiques de sécurité.

---

## 1. Vérification de l'Adressage Dynamique (DHCP)
L'objectif est de confirmer que les serveurs DHCP (Switch L3 au Siège et Routeur au Dépôt) distribuent correctement les IPs en respectant les exclusions.

* **Commande exécutée :** `ipconfig /all` sur les PC de chaque site.
* **Points de contrôle :** * Attribution d'une IP commençant à `.6` (Vérification de l'exclusion `.1` à `.5`).
    * Présence de la passerelle par défaut correcte (`.1`).
    * Attribution du serveur DNS Google (`8.8.8.8`).

> <img width="1460" height="412" alt="image" src="https://github.com/user-attachments/assets/68632cca-cc0b-44ea-896b-f39d58de8f65" />


---

## 2. Contrôle d'Accès et Sécurité (ACL)
Preuve que les règles de filtrage sont actives et bloquent les flux non autorisés vers les ressources critiques.

* **Commande exécutée :** `show access-lists` sur le Switch L3 (Siège).
* **Points de contrôle :** * **ACL 101 :** Incrémentation des "matches" sur la ligne `deny` lors d'une tentative de ping du VLAN 40 (Guest) vers l'imprimante (`192.168.20.2`).
    * **ACL 10 (VTY) :** Vérification du blocage des tentatives SSH provenant de VLANs non-administrateurs.

> <img width="1628" height="646" alt="image" src="https://github.com/user-attachments/assets/843c7733-83bc-4535-a45c-7908cb116f3a" />


---

## 3. Translation d'Adresses (NAT/PAT)
Vérification que le Routeur Central masque correctement les adresses privées pour permettre la sortie vers Internet.

* **Commande exécutée :** `show ip nat translations` sur le Routeur Central.
* **Points de contrôle :** * Présence des translations pour le réseau Siège (`192.168.x.x`).
    * Présence des translations pour le réseau Dépôt (`172.16.x.x`).
    * Vérification que l'adresse "Inside Global" correspond bien à l'IP de l'interface Serial.

><img width="1629" height="686" alt="image" src="https://github.com/user-attachments/assets/991fdf99-243e-4a96-9d56-8de5bf3848a1" />


---

## 4. Tests de Connectivité de Bout en Bout
Validation finale du routage inter-sites et de la connectivité WAN.

* **Test A (Inter-Site) :** PC-ADMIN (Siège) ↔ PC-PARTNERS (Dépôt) : **Succès**
* **Test B (Accès Internet) :** PC Dépôt (VLAN 70) ↔ 8.8.8.8 (Loopback ISP) : **Succès**
* **Test C (Accès Internet) :** PC Siège (VLAN 10) ↔ 8.8.8.8 (Loopback ISP) : **Succès**

><img width="1584" height="579" alt="image" src="https://github.com/user-attachments/assets/76147a0f-8c7a-4b03-8410-0649e0b48ddd" />


---
