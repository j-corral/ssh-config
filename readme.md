# Configuration SSH par clé

Guide pratique en français pour se connecter depuis Linux ou macOS à un serveur, directement ou via un serveur rebond, avec des clés SSH protégées par une passphrase.

## Démarrage rapide

👉 [SSH via un rebond — 2 min chrono](quickstart.md).

1. Créer une clé : `ssh-keygen -t ed25519`.
2. Installer sa partie publique sur le serveur : `ssh-copy-id -i ~/.ssh/id_ed25519.pub utilisateur@serveur`.
3. Adapter un exemple du dossier `template/`, puis intégrer les blocs utiles dans `~/.ssh/config`.
4. Se connecter avec `ssh nom-du-host`.

Les exemples contiennent des valeurs fictives à remplacer. Ils ne sont pas appliqués automatiquement. Ne pas écraser une configuration existante : fusionner les blocs utiles.

## Documentation

- [Clés et première connexion](docs/01-cles-et-premiere-connexion.md)
- [Serveur rebond et VM isolée](docs/02-rebond-et-vm-isolee.md)
- [Installer les clés publiques](docs/03-installer-les-cles-publiques.md)
- [IdentityFile et IdentitiesOnly](docs/04-selection-des-cles.md)
- [Passphrase, ssh-agent et ssh-add](docs/05-agent-et-passphrase.md)
- [Chargement automatique sur macOS et Linux](docs/06-chargement-automatique.md)
- [Vérification et dépannage](docs/07-verification-et-depannage.md)

## Exemples

| Fichier | Scénario |
| --- | --- |
| [01-direct.conf](template/01-direct.conf) | Connexion directe |
| [02-rebond-cle-commune.conf](template/02-rebond-cle-commune.conf) | Même clé pour le rebond et la VM |
| [03-rebond-cles-distinctes.conf](template/03-rebond-cles-distinctes.conf) | Une clé pour chaque serveur |
| [04-authentification-cle-uniquement.conf](template/04-authentification-cle-uniquement.conf) | Authentification par clé uniquement côté client |
| [05-macos-trousseau.conf](template/05-macos-trousseau.conf) | Rebond et trousseau macOS |
| [06-linux-agent.conf](template/06-linux-agent.conf) | Rebond et ajout automatique à un agent Linux existant |

## Principes

- Les clés privées restent sur le poste client ; seules les clés publiques sont installées sur les serveurs.
- `ProxyJump` ne nécessite pas `ForwardAgent`.
- La passphrase protège la clé privée ; le mot de passe serveur authentifie un compte distant.
- `IdentitiesOnly yes` limite les identités essayées, sans interdire le mot de passe.
- Les configurations concernent le client OpenSSH. Elles ne changent pas la politique des serveurs.

Ce dépôt contient uniquement de la documentation et des exemples : aucune clé réelle, aucune adresse réelle, aucun déploiement.

## Licence

Ce projet est distribué sous [licence MIT](LICENSE).
