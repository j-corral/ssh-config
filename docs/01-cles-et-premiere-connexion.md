# Clés et première connexion

Les commandes sont exécutées sur le poste client Linux ou macOS avec OpenSSH. Sous macOS, utiliser l'OpenSSH fourni par Apple pour les fonctions de trousseau.

## Créer une clé

Vérifier les fichiers existants avant de créer une clé pour ne pas en écraser une :

```bash
ls -la ~/.ssh
ssh-keygen -t ed25519 -f ~/.ssh/cle_vm
```

Choisir une passphrase à la demande. `cle_vm` est privée ; `cle_vm.pub` est publique. Pour des clés distinctes, refaire la commande avec `~/.ssh/cle_rebond`.

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/cle_vm
```

## Se connecter

Après installation de la clé publique :

```bash
ssh -i ~/.ssh/cle_vm utilisateur@serveur
```

Un bloc `Host` permet un alias :

```sshconfig
Host vm-directe
    HostName vm.example.invalid
    User utilisateur_vm
    IdentityFile ~/.ssh/cle_vm
    IdentitiesOnly yes
```

Le placer dans `~/.ssh/config`, puis :

```bash
chmod 600 ~/.ssh/config
ssh vm-directe
```

## Comprendre les demandes

- Empreinte de l'hôte : à la première connexion, vérifier l'empreinte via un canal fiable avant de l'accepter. Cette vérification concerne chaque serveur.
- Passphrase de la clé : déverrouillage local de la clé privée, jamais transmis au serveur.
- Mot de passe du compte distant : nécessaire si aucune clé acceptée ne permet de s'authentifier et si le serveur autorise cette méthode.

Si les clés publiques sont déjà provisionnées, une première connexion ne nécessite pas forcément de mot de passe serveur. Rien ne s'affiche lors de la saisie d'une passphrase ou d'un mot de passe dans le terminal.
