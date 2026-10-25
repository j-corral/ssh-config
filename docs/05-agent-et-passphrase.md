# Passphrase, ssh-agent et ssh-add

La passphrase chiffre la clé privée sur le disque. L'agent SSH conserve une identité déverrouillée en mémoire et réalise les opérations cryptographiques demandées par le client. La passphrase n'est pas envoyée au serveur.

```bash
ssh-add ~/.ssh/cle_rebond
ssh-add ~/.ssh/cle_vm
ssh vm-isolee
```

La passphrase est demandée lors de l'ajout. Une même clé utilisée pour les deux serveurs ne nécessite qu'un seul ajout.

## Limiter la durée et gérer l'agent

```bash
# Charger pour une heure
ssh-add -t 1h ~/.ssh/cle_vm

# Lister les empreintes des identités chargées
ssh-add -l

# Retirer une identité
ssh-add -d ~/.ssh/cle_vm

# Retirer toutes les identités de cet agent
ssh-add -D
```

La protection sur le disque reste en place. Une fois la durée expirée, la clé est retirée de l'agent ; un trousseau configuré peut toutefois la déverrouiller et la charger de nouveau.

## Si aucun agent n'est disponible

Les sessions graphiques fournissent souvent un agent. Si `ssh-add` indique qu'il ne peut pas se connecter à l'agent, vérifier d'abord son environnement :

```bash
printf '%s\n' "$SSH_AUTH_SOCK"
```

Dans un terminal sans agent, on peut en lancer un pour ce shell :

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/cle_vm
```

Éviter de lancer un nouvel agent à chaque ouverture de terminal : ils ne partageraient pas nécessairement leurs identités.

## Après redémarrage

Un agent classique perd ses clés chargées au redémarrage ou à sa fermeture. Sans trousseau, il faut déverrouiller les clés à nouveau. Il est toujours possible de lancer directement `ssh vm-isolee` : SSH demandera la passphrase au besoin, sans passage préalable obligatoire par `ssh-add`.
