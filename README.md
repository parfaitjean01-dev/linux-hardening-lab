# Linux Hardening Lab

## Objectif
Mettre en place un durcissement de base d’un système Linux (serveur) en appliquant des bonnes pratiques de cybersécurité.

## Contexte
Projet personnel réalisé dans le cadre d’une candidature en Master Cybersécurité.

## Environnement
- OS : Ubuntu
- Type : Machine virtuelle
- Accès : SSH

## Plan du projet
1. Mise à jour du système
2. Sécurisation SSH
3. Pare-feu (UFW)
4. Protection contre le brute-force (Fail2Ban)
5. Audit basique (ports, logs)
6. Documentation et preuves

## Références
- Bonnes pratiques de sécurité (ANSSI – hygiène informatique)

## Configuration du système

- Distribution : Ubuntu
- Noyau : Linux (WSL)
- Système mis à jour via `apt update` / `apt upgrade`
- Objectif : Mise en place d’une base sécurisée avant le durcissement

## Jour 3 – Utilisateur administrateur

- Création d’un utilisateur administrateur non-root : `administrator`
- Attribution des privilèges sudo
- Vérification de l’accès administrateur (`sudo whoami` → root)

## Jour 4 – Sécurisation SSH

- Installation du serveur OpenSSH
- Activation via systemd socket (`ssh.socket`)
- Désactivation de l’accès root
- Désactivation de l’authentification par mot de passe
- Authentification par clé uniquement
- Restriction des utilisateurs autorisés (`AllowUsers`)
- Réduction des tentatives d’authentification
- Validation de la configuration avec `sshd -t`

## Jour 5 – Authentification SSH par clé

- Génération d’une clé SSH ED25519 côté client
- Installation de la clé publique dans `authorized_keys` pour l’utilisateur `administrator`
- Permissions sécurisées :
  - `~/.ssh` : 700
  - `authorized_keys` : 600
- Authentification par mot de passe désactivée
- Accès SSH autorisé uniquement par clé publique


