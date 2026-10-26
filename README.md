# Mini-OS 1.0.0 — x86_64-linux

Binaire **statique** (aucune dépendance) + site + installateur.

## Installation

```bash
tar xzf minios-1.0.0-x86_64-linux.tar.gz
cd minios-1.0.0-x86_64-linux
sudo ./install.sh
```

`install.sh` crée l'utilisateur système `minios`, installe le binaire et le
site dans `/var/lib/minios/`, configure systemd, `acl` et le mot de passe
administrateur (une seule fois), puis démarre le service.

- Site : http://127.0.0.1:8080/app
- Données : `/var/lib/minios/`
- Le site se modifie directement dans `/var/lib/minios/mini-os.html`, sans
  recompiler ni redémarrer.
- Un `install.sh` ultérieur **conserve** le site existant ; utiliser
  `sudo MINIOS_UPDATE_HTML=1 ./install.sh` pour le remplacer.

## Contenu

| Fichier | Rôle |
|---|---|
| `minios-backend` | binaire statique x86_64-linux |
| `mini-os.html` | site (lu à chaque requête) |
| `install.sh` | installation / mise à jour |
| `SHA256SUMS` | sommes de contrôle |

## Vérification

Depuis le dossier où les fichiers ont été téléchargés :

```bash
sha256sum -c SHA256SUMS
```

## Désinstallation

```bash
sudo systemctl disable --now minios
sudo rm -rf /var/lib/minios /etc/minios.env \
            /etc/systemd/system/minios.service /etc/sudoers.d/minios-acl
sudo systemctl daemon-reload
sudo userdel minios   # facultatif : supprime aussi son dossier personnel
```

Version de l'API : `GET /api/v1/version` → `{"version":"1.0.0"}`.
