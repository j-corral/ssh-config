# IdentityFile et IdentitiesOnly

```sshconfig
IdentityFile ~/.ssh/cle_vm
IdentitiesOnly yes
```

`IdentityFile` indique une identité à utiliser. `IdentitiesOnly yes` limite les identités proposées à celles configurées, même si l'agent contient d'autres clés. Plusieurs directives `IdentityFile` applicables peuvent se cumuler : ce n'est pas une garantie qu'un seul fichier sera essayé dans une configuration composée de plusieurs blocs.

Avec `IdentitiesOnly no`, valeur par défaut, SSH peut également proposer les autres identités de l'agent. Trop de clés peuvent épuiser la limite de tentatives du serveur et provoquer `Too many authentication failures` avant que la bonne identité ne soit essayée.

## Limiter les méthodes à la clé côté client

```sshconfig
PreferredAuthentications publickey
```

Cela empêche le client d'essayer le mot de passe ou le clavier interactif pour cet hôte. La passphrase locale de la clé peut toujours être demandée. Pour un rebond, ajouter cette option aux deux blocs si l'on souhaite appliquer ce comportement aux deux authentifications.

`IdentitiesOnly yes` seul n'interdit pas le mot de passe. `PasswordAuthentication no` seul ne désactive pas nécessairement une demande par clavier interactif.

Une politique imposée à tous les clients nécessite une configuration de `sshd` sur chaque serveur, distincte de ce guide. Une authentification multifacteur exigée par le serveur peut être incompatible avec une restriction client à `publickey` uniquement.

## Ordre des blocs

Pour la plupart des options, la première valeur obtenue est retenue : placer les blocs spécifiques avant les valeurs générales `Host *`. Examiner la configuration effective avec `ssh -G alias`.
