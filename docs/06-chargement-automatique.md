# Chargement automatique : macOS et Linux

Distinguer le démarrage de l'agent, le chargement d'une clé et le déverrouillage de sa passphrase. `AddKeysToAgent yes` ajoute une clé à un agent existant au moment où SSH la charge : cela ne démarre pas l'agent et ne signifie pas que la clé est chargée dès le démarrage du PC.

## macOS : trousseau Apple

Avec les outils OpenSSH fournis par Apple, ajouter dans chacun des blocs `Host rebond` et `Host vm-isolee` :

```sshconfig
    AddKeysToAgent yes
    UseKeychain yes
```

Puis exécuter une fois :

```bash
/usr/bin/ssh-add --apple-use-keychain ~/.ssh/cle_rebond
/usr/bin/ssh-add --apple-use-keychain ~/.ssh/cle_vm
```

La passphrase est enregistrée dans le trousseau. Lors des connexions suivantes, y compris après redémarrage, SSH peut la récupérer si le trousseau est déverrouillé et accessible. Une demande peut réapparaître si celui-ci est verrouillé ou si ses règles d'accès l'exigent.

La clé peut être chargée au premier usage après ouverture de session ; aucun script de démarrage n'est nécessaire pour ce fonctionnement. Les options Apple peuvent être absentes d'un OpenSSH installé séparément. Les anciennes versions antérieures à macOS 12 utilisent d'autres noms d'options de `ssh-add`.

## Linux : agent et trousseau de la session

Ajouter dans les deux blocs :

```sshconfig
    AddKeysToAgent yes
```

Avec un agent accessible, la clé est ajoutée après son déverrouillage lors de la première utilisation. Sans trousseau, la passphrase reste à saisir après chaque nouvelle session d'agent.

Pour un déverrouillage sans ressaisie après redémarrage, configurer l'intégration au trousseau de l'environnement de bureau. Selon la distribution, la version et le bureau, les composants diffèrent : GNOME peut utiliser GCR/GNOME Keyring ; KDE possède son propre environnement de trousseau, dont l'intégration SSH doit être vérifiée. Le trousseau doit lui-même être déverrouillé à l'ouverture de session ; une connexion automatique au bureau ne garantit pas cela.

Il n'existe pas de commande universelle applicable à tous les bureaux Linux. Identifier l'environnement avant de configurer le démarrage :

```bash
cat /etc/os-release
printf '%s\n' "$XDG_CURRENT_DESKTOP"
printf '%s\n' "$SSH_AUTH_SOCK"
ssh-add -l
```

Ne pas mettre une passphrase en clair dans un script de démarrage. Le modèle Linux fourni ajoute les clés à un agent existant, sans prétendre configurer un trousseau spécifique.

## Configuration partagée entre les deux systèmes

Placer `IgnoreUnknown UseKeychain` avant la première occurrence de `UseKeychain` permet aux clients OpenSSH qui ne connaissent pas cette option de l'ignorer. Cela ne leur apporte pas l'intégration au trousseau Apple.

## Sources

- [OpenSSH : AddKeysToAgent](https://man.openbsd.org/ssh_config#AddKeysToAgent)
- [Apple : UseKeychain et ajout à l'agent](https://developer.apple.com/library/archive/technotes/tn2449/_index.html)
- [GitHub : commandes macOS ssh-add](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent?platform=mac)
- [GNOME : clés SSH et agent](https://handbook.gnome.org/infrastructure/ssh-keys.html)
