# PhoneCare Server

Application web de gestion de réparations mobiles : dossiers atelier, journal d'intervention, export CSV, guide des procédures officielles et contrôle d'accès par jeton administrateur.

> **Limite de sécurité :** PhoneCare ne contourne pas Activation Lock/iCloud, Google FRP, Samsung Account/Knox, MDM ou les autres protections antivol. Il ne contient ni exploit ni commande de contournement. Les déverrouillages doivent être réalisés par le propriétaire ou par le support / administrateur autorisé.

## Fonctionnalités
- Interface web en français, adaptée au mobile et au bureau.
- Création et suivi de dossiers de réparation.
- Types de problèmes : iCloud, Google FRP, Samsung/Knox/MDM, Huawei, Xiaomi, Motorola, OPPO, pilotes USB, firmware et autres diagnostics.
- Notes de diagnostic et journal horodaté par dossier.
- Export CSV des dossiers et export texte des événements d'administration.
- Authentification administrateur par secret défini dans l'environnement.
- Docker et Docker Compose.
- GitHub Actions pour vérifier la syntaxe Python.

## Déploiement avec Docker Compose (recommandé)
Sur un serveur Linux avec Docker Engine et le plugin Docker Compose :

```bash
git clone https://github.com/VOTRE-COMPTE/PhoneCare-Server.git
cd PhoneCare-Server
cp .env.example .env
```

Générez un secret fort et placez-le dans `.env` :
```bash
openssl rand -hex 32
```
Définissez aussi `PHONECARE_SESSION_SECRET` avec une valeur aléatoire différente. **Ne publiez jamais le fichier `.env`.**

Puis lancez :
```bash
docker compose up -d --build
docker compose logs -f
```

L'application écoute uniquement sur `127.0.0.1:8000` sur l'hôte. Pour l'accès distant, configurez un reverse proxy HTTPS (Caddy, Nginx ou équivalent) devant elle. N'exposez pas directement le service à Internet sans TLS et règles de sécurité.

Vérifiez le service :
```bash
curl http://127.0.0.1:8000/health
```

## Déploiement local sans Docker
Python 3.11+ :
```bash
python -m venv .venv
# Windows PowerShell :
.venv\Scripts\Activate.ps1
# macOS/Linux :
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
```
Définissez les variables `PHONECARE_ADMIN_TOKEN` et `PHONECARE_SESSION_SECRET` dans l'environnement, puis :
```bash
uvicorn app.main:app --host 127.0.0.1 --port 8000
```

## Publier sur GitHub
1. Créez un nouveau dépôt GitHub vide, par exemple `PhoneCare-Server`.
2. Décompressez cette archive.
3. Depuis le dossier du projet :
```bash
git init
git add .
git commit -m "Initial PhoneCare Server"
git branch -M main
git remote add origin https://github.com/VOTRE-COMPTE/PhoneCare-Server.git
git push -u origin main
```
4. Ne commitez jamais `.env`, les bases de données ou des informations clients. Le `.gitignore` les exclut.

## USB et Wi-Fi : limite importante
Une application hébergée sur un serveur ne peut pas accéder directement aux ports USB du PC de l'atelier. Cette version gère les dossiers et journaux côté serveur. Pour exécuter ADB/Fastboot sur un poste Windows, il faudra ajouter un agent local distinct, avec autorisation explicite et liste de commandes strictement limitée. La connexion ADB Wi-Fi exige que le débogage sans fil ait été activé et autorisé sur le téléphone.

## Données, confidentialité et sauvegardes
- SQLite persiste dans un volume Docker.
- Sauvegardez régulièrement le volume `phonecare_data`.
- Ne saisissez pas de mots de passe, codes de déverrouillage ou données personnelles superflues.
- Cette version est une base mono-administrateur pour petit atelier, non auditée pour un déploiement multi-utilisateur à grande échelle.
- Pour un usage professionnel public, ajoutez comptes individuels, MFA, limitation de débit, politique de conservation, sauvegardes testées et audit de sécurité.

## Licence
MIT — voir `LICENSE`.
