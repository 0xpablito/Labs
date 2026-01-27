#  Tests de Validation : Phase 1 - Configuration de base & Sécurité

Ce document regroupe les tests techniques validant la configuration de sécurité et l'administration des équipements.

---

## 1. Sécurité du système et Chiffrement 
L'objectif est de vérifier que les mots de passe ne sont pas stockés en clair et que le mode privilégié est protégé.

* **Commande exécutée :** `show running-config`
* **Points de contrôle :**  Présence du `enable secret`.
    * Chiffrement des mots de passe `line con 0`.

> <img width="629" height="44" alt="image" src="https://github.com/user-attachments/assets/b59928f7-6994-4dc3-96d9-d0e335200e14" />





---

## 2. Administration à distance (SSHv2) 
Vérification du protocole de gestion sécurisée et de l'accès via le compte administrateur.

* **Commande exécutée :** `show ip ssh`
* **Résultat attendu :** SSH Enabled - version 2.0.

> <img width="633" height="49" alt="image" src="https://github.com/user-attachments/assets/a978dfff-e72a-4367-8d15-7a6d4aec1680" />

---

## 3. Test de connexion "End-to-End" via SSH 
Validation de l'accès depuis un poste d'administration et affichage de la bannière de sécurité.

* **Action :** Connexion depuis le terminal d'un PC (Packet Tracer).
* **Commande :** `ssh -l admin 192.168.10.1`
* **Points de contrôle :**
    1. Affichage de la bannière **MOTD**.
    2. Demande d'authentification (Compte local).
    3. Accès réussi au prompt du switch.

> <img width="764" height="162" alt="image" src="https://github.com/user-attachments/assets/d7947a10-0d4c-4589-b733-7a44a6a26197" />


---

## 4. Blocage des protocoles non sécurisés 
Preuve que le protocole Telnet est désactivé au profit du SSH.

* **Commande :** `telnet 192.168.1.10`
* **Résultat attendu :** "Connection closed by foreign host" ou "Connection refused".

> <img width="642" height="60" alt="image" src="https://github.com/user-attachments/assets/0508eb33-8598-4cd5-bbf6-08fe1af96b8c" />
