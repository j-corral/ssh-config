# Vérification et dépannage

## Vérifier la configuration sans connexion

```bash
ssh -G vm-isolee
```

Pour inspecter un exemple sans modifier `~/.ssh/config` :

```bash
ssh -F template/03-rebond-cles-distinctes.conf -G rebond
ssh -F template/03-rebond-cles-distinctes.conf -G vm-isolee
```

`-G` affiche la configuration effective sans ouvrir une session réseau. Cela ne valide ni la connectivité ni l'acceptation de la clé par le serveur.

## Diagnostiquer une vraie connexion

```bash
ssh -v vm-isolee
ssh-add -l
```

Les traces peuvent contenir des informations d'infrastructure : les relire avant de les partager.

| Symptôme | Points à vérifier |
| --- | --- |
| `Permission denied (publickey)` | Utilisateur, clé sélectionnée, clé publique installée sur le bon compte, permissions distantes, politique serveur |
| `Too many authentication failures` | Agent contenant trop de clés ; `IdentitiesOnly yes` et identités effectivement configurées |
| Impossible de joindre l'agent | Agent démarré et `SSH_AUTH_SOCK` disponible dans ce terminal |
| `administratively prohibited` | Autorisation de transfert TCP et restrictions de destination sur le rebond |
| Timeout vers la VM | Routage et pare-feu entre rebond et VM ; port SSH de la VM |
| `Bad configuration option: usekeychain` | Option réservée à Apple ; utiliser son OpenSSH ou placer `IgnoreUnknown UseKeychain` avant l'option |
| Passphrase redemandée après redémarrage | Identité perdue de l'agent, absence de trousseau, trousseau verrouillé ou intégration non configurée |

Si l'empreinte d'un serveur change, vérifier la cause par un canal fiable avant de modifier `known_hosts`.

## Permissions usuelles

Sur le poste client, pour les fichiers réellement présents :

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/config ~/.ssh/cle_rebond ~/.ssh/cle_vm
```

Sur chaque compte distant : répertoire `~/.ssh` en `700`, fichier `authorized_keys` en `600`, appartenant au bon utilisateur. Ne pas modifier récursivement tout le dossier personnel.

## Références OpenSSH

- [Configuration du client](https://man.openbsd.org/ssh_config)
- [ssh](https://man.openbsd.org/ssh)
- [ssh-add](https://man.openbsd.org/ssh-add)
- [ssh-agent](https://man.openbsd.org/ssh-agent)
