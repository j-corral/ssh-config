# Installer les clés publiques

Ces commandes modifient `~/.ssh/authorized_keys` sur le compte distant. Elles supposent un accès initial par mot de passe autorisé, ou un autre accès déjà fonctionnel. Sans accès initial, un administrateur doit provisionner la clé par une console ou un autre moyen autorisé.

## Sur un serveur direct

```bash
ssh-copy-id -i ~/.ssh/cle_vm.pub utilisateur_vm@serveur
```

## Avec les alias rebond et vm-isolee

Installer d'abord la clé du rebond, puis celle de la VM :

```bash
ssh-copy-id -i ~/.ssh/cle_rebond.pub rebond
ssh-copy-id -i ~/.ssh/cle_vm.pub vm-isolee
```

La deuxième commande utilise le `ProxyJump` de `~/.ssh/config`. Le mot de passe du compte VM peut être demandé pendant l'installation ; le rebond peut aussi demander une authentification si nécessaire.

Ne pas activer une configuration client limitée à `publickey` avant d'avoir installé et testé les clés : elle empêcherait l'accès initial par mot de passe.

## Sans alias pour la VM

Si le rebond utilise une identité par défaut ou une identité déjà disponible dans l'agent :

```bash
ssh-copy-id -i ~/.ssh/cle_vm.pub \
  -o ProxyJump=utilisateur_rebond@rebond.example.invalid \
  utilisateur_vm@10.0.0.10
```

L'option `-i` de `ssh-copy-id` désigne la clé publique à installer. Elle ne sélectionne pas une clé privée distincte pour le rebond : utiliser son bloc `Host` pour cela.

## Si ssh-copy-id n'est pas disponible

Avec les alias configurés :

```bash
cat ~/.ssh/cle_rebond.pub | ssh rebond 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys'
cat ~/.ssh/cle_vm.pub | ssh vm-isolee 'umask 077; mkdir -p ~/.ssh; cat >> ~/.ssh/authorized_keys; chmod 700 ~/.ssh; chmod 600 ~/.ssh/authorized_keys'
```

Cette alternative ajoute la clé en fin de fichier ; la répéter peut créer des doublons. Elle suppose un shell distant compatible et ne concerne que le compte connecté.

## Tester

```bash
ssh -o PreferredAuthentications=publickey rebond
ssh -o PreferredAuthentications=publickey vm-isolee
```

Ne désactiver les méthodes serveur par mot de passe qu'après validation d'une nouvelle connexion par clé et conservation d'un accès de secours. La configuration de `sshd` n'est pas effectuée par ce dépôt.
