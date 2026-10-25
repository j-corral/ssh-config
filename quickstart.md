# SSH via un rebond — 2 min chrono

**Objectif :** Linux ou Mac → rebond → VM isolée, avec les clés privées sur ton poste.

Pré requis : accès initial par mot de passe aux deux comptes et port SSH de la VM accessible depuis le rebond. Toutes les commandes ci-dessous se lancent sur ton poste.

## 1. Créer les clés

Si tu possèdes déjà ces clés, passe à l'étape 2. Ne remplace pas une clé existante.

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
ssh-keygen -t ed25519 -f ~/.ssh/cle_rebond
ssh-keygen -t ed25519 -f ~/.ssh/cle_vm
```

Choisis une passphrase pour chaque clé.

## 2. Configurer les alias

Ajoute ces blocs à `~/.ssh/config` (sans écraser son contenu). Remplace l'adresse du rebond, l'IP privée de la VM et les utilisateurs :

```sshconfig
Host rebond
    HostName rebond.example.invalid
    User utilisateur_rebond
    IdentityFile ~/.ssh/cle_rebond
    IdentitiesOnly yes
    AddKeysToAgent yes

Host vm-isolee
    HostName 10.0.0.10
    User utilisateur_vm
    IdentityFile ~/.ssh/cle_vm
    IdentitiesOnly yes
    AddKeysToAgent yes
    ProxyJump rebond
```

```bash
chmod 600 ~/.ssh/config
```

## 3. Installer les clés publiques

```bash
ssh-copy-id -i ~/.ssh/cle_rebond.pub rebond
ssh-copy-id -i ~/.ssh/cle_vm.pub vm-isolee
```

Saisis les mots de passe des comptes distants si demandés. À la première connexion, vérifie l'empreinte de chaque serveur avant de l'accepter.

Sur Mac, si `ssh-copy-id` est absent, utilise ces commandes à la place :

```bash
cat ~/.ssh/cle_rebond.pub | ssh rebond 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys'
cat ~/.ssh/cle_vm.pub | ssh vm-isolee 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys'
```

## 4. Se connecter

```bash
ssh vm-isolee
```

Ton poste peut demander les passphrases des clés. Avec un agent disponible, `AddKeysToAgent yes` évite de les retaper pendant la session.

**Après redémarrage :** [configurer le trousseau macOS ou Linux](docs/06-chargement-automatique.md).

**Un souci ?** [Vérification et dépannage](docs/07-verification-et-depannage.md).
