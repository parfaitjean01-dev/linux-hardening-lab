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

## Audit basique (ports, services et logs)

### Vérifications effectuées

- Analyse des ports ouverts afin d’identifier les services exposés sur le système
- Vérification des services actifs via systemd
- Contrôle spécifique du service SSH (état, écoute, configuration effective)
- Analyse des journaux SSH pour détecter d’éventuelles tentatives d’accès ou erreurs d’authentification

### Preuves collectées

Les preuves de l’audit sont stockées dans le répertoire suivant :

- `preuves/audit/ports_ss.txt` : ports ouverts (commande `ss`)
- `preuves/audit/ports_ss_sudo.txt` : ports ouverts avec privilèges administrateur
- `preuves/audit/services_running.txt` : services systemd actifs
- `preuves/audit/ssh_status.txt` : état détaillé du service SSH
- `preuves/audit/sshd_effective_config.txt` : configuration SSH réellement appliquée
- `preuves/audit/ssh_journal_200.txt` : derniers événements SSH
- `preuves/audit/ssh_failures_tail50.txt` : tentatives d’authentification échouées

### Conclusion

- Le système n’expose qu’un nombre limité de ports
- Le service SSH est actif et fonctionnel
- L’accès root est désactivé via SSH
- L’authentification par mot de passe est désactivée
- L’accès SSH est autorisé uniquement par clé publique

L’audit confirme que la surface d’attaque est réduite et conforme aux bonnes pratiques de sécurisation de base.

## Pare-feu (UFW)

### Objectif

Réduire la surface d’attaque du système en appliquant une politique de filtrage réseau restrictive, conforme aux bonnes pratiques de sécurisation de base.

### Mise en place

- Installation du pare-feu UFW
- Application d’une politique restrictive par défaut :
  - Refus de toutes les connexions entrantes
  - Autorisation des connexions sortantes
- Autorisation explicite du service SSH afin de conserver l’accès administrateur

### Règles appliquées

- Politique par défaut :
  - `deny incoming`
  - `allow outgoing`
- Port autorisé :
  - SSH (22/tcp)

### Vérification

État du pare-feu après configuration :

- Pare-feu actif et activé au démarrage
- Aucune exposition de service inutile
- Accès distant limité strictement au service SSH

### Conclusion

La configuration du pare-feu permet de limiter efficacement les risques d’exposition réseau en n’autorisant que le strict nécessaire. Cette étape réduit significativement la surface d’attaque du système et constitue une base solide pour un environnement sécurisé.

## Protection contre les attaques par force brute (Fail2Ban)

### Objectif

Protéger le service SSH contre les tentatives d’authentification répétées en bloquant automatiquement les adresses IP suspectes.

### Mise en place

- Installation de Fail2Ban
- Activation du jail SSH (`sshd`)
- Limitation du nombre de tentatives d’authentification
- Blocage temporaire des adresses IP en cas d’échec répété

### Paramètres appliqués

- Nombre maximum de tentatives : 3
- Fenêtre d’observation : 10 minutes
- Durée de bannissement : 10 minutes

### Vérification

- Service Fail2Ban actif
- Jail SSH actif et surveillant les journaux d’authentification

### Conclusion

Fail2Ban permet de réduire efficacement le risque d’attaques par force brute sur le service SSH en appliquant des blocages automatiques et temporaires.

## Audit et vérifications

### Ports ouverts
- Vérification avec `ss -tulpen`
- Seul le port SSH (22/tcp) est exposé

### Pare-feu (UFW)
- Politique par défaut restrictive
- Connexions entrantes refusées
- SSH explicitement autorisé

### Configuration SSH effective
- Accès root désactivé
- Authentification par mot de passe désactivée
- Authentification par clé uniquement
- Accès limité à l’utilisateur `administrator`

### Protection contre le brute-force
- Fail2Ban actif sur le service SSH
- Surveillance du fichier `/var/log/auth.log`
- Bannissement automatique après tentatives échouées

## CONCLUSION GENERALE DU PROJET

Ce projet a permis de mettre en place une base de sécurisation robuste pour un système Linux accessible à distance via SSH.

Les principales surfaces d’attaque ont été réduites grâce à :
- la suppression de l’accès root à distance,
- la désactivation de l’authentification par mot de passe,
- l’utilisation exclusive de clés SSH pour l’accès administrateur,
- l’application d’un pare-feu restrictif avec UFW,
- la protection contre les attaques par force brute via Fail2Ban.

Les audits réalisés et les preuves collectées confirment que seuls les services strictement nécessaires sont exposés et que les mécanismes de sécurité sont correctement appliqués.

Cette configuration constitue une base saine avant d’envisager des mesures de durcissement plus avancées, telles que la supervision, la centralisation des journaux, le durcissement du noyau ou l’ajout de mécanismes de détection d’intrusion.
