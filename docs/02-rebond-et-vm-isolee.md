# Serveur rebond et VM isolée

Trajet : poste client → serveur rebond → VM isolée.

```bash
ssh -J utilisateur_rebond@rebond.example.invalid utilisateur_vm@10.0.0.10
```

Le poste doit joindre le rebond. Le rebond doit joindre le port SSH de la VM et sa configuration serveur doit autoriser ce transfert TCP vers la destination.

## Configuration recommandée avec deux clés

```sshconfig
Host rebond
    HostName rebond.example.invalid
    User utilisateur_rebond
    IdentityFile ~/.ssh/cle_rebond
    IdentitiesOnly yes

Host vm-isolee
    HostName 10.0.0.10
    User utilisateur_vm
    IdentityFile ~/.ssh/cle_vm
    IdentitiesOnly yes
    ProxyJump rebond
```

```bash
ssh vm-isolee
```

C'est `ssh vm-isolee`, avec une espace, et non `ssh-vm`.

Le client s'authentifie sur le rebond, ouvre un transfert vers la VM, puis s'authentifie sur celle-ci depuis le poste local. Chaque serveur possède sa propre clé d'hôte, à vérifier.

Les deux clés privées restent sur le poste. Il n'est pas nécessaire de copier une clé privée sur le rebond ni d'activer `ForwardAgent`.

On peut utiliser une même clé pour les deux serveurs en installant sa partie publique sur chacun et en utilisant le même `IdentityFile`. Avec deux clés protégées distinctes, deux passphrases peuvent être demandées si elles ne sont pas déjà prises en charge par l'agent ou le trousseau.

Les options du bloc de la VM ne sont pas automatiquement celles du rebond : configurer les deux blocs. Pour un port différent, ajouter `Port 2222` au bloc concerné.
