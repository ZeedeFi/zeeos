# Mini-OS 1.1.0 — x86_64 Linux (Debian / Ubuntu)

Dossier prét à uploader : binaire statique + site complet + installateur.

## Contenu de la release « 1,1 »

| Fichier | Rôle |
|---|---|
| `minios-backend` | binaire statique x86_64 (musl, aucune dépendance) |
| `mini-os.html`   | site complet (upload robuste, disques, partitions, terminal…) |
| `setup.sh`       | installateur tout-en-un Debian/Ubuntu |
| `SHA256SUMS`     | sommes de contrôle |

## Installation (une seule commande)

```bash
curl -fsSL "https://github.com/ZeedeFi/zeeos/releases/download/1%2C1/setup.sh" -o setup.sh
sudo bash setup.sh
```

Le script fait tout :

1. Verrou **Debian/Ubuntu uniquement** (vérifie apt + `/etc/debian_version`)
2. Installe **toutes** les dépendances (29 paquets) : parted, dosfstools,
   e2fsprogs, mtools, ntfs-3g, exfatprogs, f2fs-tools, xfsprogs, btrfs-progs,
   udftools, jfsutils, nilfs-tools, reiserfsprogs, lvm2, cryptsetup, udisks2,
   cifs-utils, nfs-common, aria2, curl, p7zip-full, iputils-ping, traceroute,
   acl, coreutils, util-linux, sudo, bash
3. Télécharge le binaire + le site, **vérifie SHA-256** (anti-corruption)
4. Crée l'utilisateur de service `minios` (ajouté au groupe `disk`)
5. Installe dans `/var/lib/minios` (site modifiable sans sudo)
6. Demande le **mot de passe administrateur** (saisie masquée, confirmation,
   génération aléatoire possible)
7. Configure le service systemd avec `CAP_SYS_ADMIN` + groupe `disk`
   (montage + partitionnement sans root) et `MINIOS_ALLOW_FORMAT=1`
8. Affiche le **tableau de vérifications finales ✅/❌**

## Mise à jour / réinstallation

```bash
sudo bash setup.sh                      # conserve mot de passe et site existant
sudo MINIOS_UPDATE_HTML=1 bash setup.sh # remplace le site
```

Personnalisation (facultatif) :

```bash
sudo MINIOS_PASSWORD="secret" MINIOS_BIND=0.0.0.0:9000 bash setup.sh
sudo MINIOS_APP_DIR=/opt/minios bash setup.sh        # autre dossier
sudo MINIOS_NO_FS_DEPS=1 bash setup.sh               # sans apt (tests)
```

## Utilisation

- Site : `http://<IP>:8080/app` (LAN direct si bind 0.0.0.0)
- Login : ton mot de passe administrateur
- Gestion fichiers : upload par morceaux 8 Mio (CRC32, reprise, débit/ETA),
  refus propre des fichiers > 4 Go sur FAT32
- Disques : toutes partitions visibles (multi-partition), montage/démontage
  par partition, disques système + montages protégés refusés
- GParted intégré : table GPT/MSDOS, création/suppression/redimensionnement,
  formatage (16 FS), flags, check, label, wipe — confirmation tape le nom, dry-run possible
- Téléchargements aria2, archives 7z/zip, terminal web, ping/traceroute

## Vérification après setup

```bash
systemctl status minios
curl -I http://127.0.0.1:8080/app      # HTTP/1.1 200 OK attendu
```

## Sécurité

- Formatage/partitionnement exige `MINIOS_ALLOW_FORMAT=1` (posé par le setup)
- Disques système et montages `/boot /usr /var /etc /opt /root /srv…
  refusés → 403 PROTECTED_MOUNT
- `auth.json` en 0600, uploads en fichier temporaire + rename atomique
- Mots de passe chiffrés Argon2, sessions expirantes

© Mini-OS 1.0.0
