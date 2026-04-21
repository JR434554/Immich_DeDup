# 🧹 Immich Duplicate Cleaner (english, french below)

Python script to intelligently detect and delete **duplicate photos/videos** on a [Immich](https://github.com/immich-app/immich) server, prioritizing heic (Apple) files over size.

---

## ✨ Main features

- 🔍 Automatic recovery of duplicates via the Immich API
- 📸 Intelligent file sorting by :
  1. **Date taken** (`exif.dateTimeOriginal`)
  2. **Preferred format** : `.heic` in priority
  3. **File's size** (we keep the largest)
  4. **Richness of EXIF metadata**
- 📋 **Only pairs mode** – process only groups with exactly 2 files (e.g. JPG+HEIC); skip series with 3+ for manual selection
- 🔄 **Metadata transfer** – optionally transfer albums, tags, and location/EXIF from deleted assets to the kept one (augment only, never overwrite)
- 🧪 **Simulation mode** to test without deleting, useful for viewing logs
- ✔️ **Confirm mode** – interactive per-group approval [Y/n]; with DRY_RUN=true only logs, with DRY_RUN=false processes immediately
- 🗑️ Option to delete to the recycle bin or permanently
- 📄 Automatic logging to a `.log` file (optional)

---

## ⚙️ Prerequisites

- Immich server operational (self-hosted or public)
- A valid **API key**
- Python ≥ 3.7

### API Key and Permissions

Create an API key in Immich under **Settings → API Keys**. The key needs the following permissions:

| Permission | Purpose |
|------------|---------|
| `duplicate.read` | Retrieve duplicate groups |
| `asset.delete` | Delete duplicate assets |
| `asset.update` | Transfer location/EXIF metadata (when `IMMICH_TRANSFER_METADATA=true`) |
| `album.read` | Read album memberships for metadata transfer |
| `album.asset.create` | Add kept asset to albums (when `IMMICH_TRANSFER_METADATA=true`) |
| `album.asset.delete` | Remove kept asset from albums (when `IMMICH_KEEP_METADATA=false`) |
| `tag.asset` | Add/remove tags for metadata transfer |

A full-access API key includes all of these. If you only use `IMMICH_TRANSFER_METADATA=false` and `IMMICH_KEEP_METADATA=true`, the minimum required permissions are `duplicate.read` and `asset.delete`.

---

## 📦 Installation

1. Clone the repository and enter the directory
2. Create a virtual environment:
   ```bash
   python -m venv .venv
   ```
3. Activate it (PowerShell on Windows):
   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```
   On Linux/macOS:
   ```bash
   source .venv/bin/activate
   ```
4. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

---

## ⚙️ Configuration

1. Copy the example env file and edit it:
   ```bash
   copy .env.example .env   # Windows
   cp .env.example .env    # Linux/macOS
   ```
2. Edit `.env` and set at least:
   - `IMMICH_SERVER` – your Immich server URL (e.g. `https://immich.example.com`)
   - `IMMICH_API_KEY` – your API key from Immich (Settings → API Keys)
   - `IMMICH_DRY_RUN` – `true` to simulate only (default), `false` to actually delete
   - `IMMICH_DEFINITELY` – `false` for recycle bin (default), `true` for permanent deletion
   - `IMMICH_ENABLE_LOG` – `true` or `false` for log file creation
   - `IMMICH_ONLY_PAIRS` – `false` (default) to process all groups, `true` to process only groups with exactly 2 files
   - `IMMICH_HEIC_ONLY` – `false` (default); when `true`, only duplicate groups with at least one `.heic` file (or `_HEVC` variant) are processed
   - `IMMICH_FILENAME_MATCH` – `false` (default); when `true`, only groups whose normalized base filenames all match are processed (e.g., `IMG_0127.JPG` with `IMG_0127-1.JPG`)
   - `IMMICH_KEEP_METADATA` – `true` (default) to let the kept image keep its metadata, `false` to remove it before transfer
   - `IMMICH_TRANSFER_METADATA` – `true` (default) to transfer albums, tags, location from deleted assets to the kept one
   - `IMMICH_CONFIRM` – `true` to ask [Y/n] before each duplicate group; `false` (default) to add all to bulk list
   - `IMMICH_REQUEST_TIMEOUT` – request timeout in seconds (default: 5); increase for slow servers
   - `IMMICH_DELETE_BATCH_SIZE` – batch size for bulk deletion (default: 500); limits payload per request

Alternatively, set these as environment variables directly instead of using a `.env` file.

---

## 🚀 Usage

```bash
python immich_duplicates_en.py
```

For the French version:

```bash
python immich_duplicates_fr.py
```

**Tip:** Run with `IMMICH_DRY_RUN=true` (default) first to see what would be deleted. Use `IMMICH_CONFIRM=true` to approve each group [Y/n]; with `DRY_RUN=true` only logs, with `DRY_RUN=false` processes immediately.

---

# 🧹 Nettoyeur de doublons Immich (français)

Script Python pour détecter et supprimer intelligemment les **doublons photos/vidéos** sur un serveur [Immich](https://github.com/immich-app/immich), en **donnant la priorité aux fichiers heic (Apple)** par rapport à la taille.

---

## ✨ Fonctionnalités principales

- 🔍 Récupération automatique des doublons via l’API Immich
- 📸 Tri intelligent des fichiers par :
  - **Date de capture** (`exif.dateTimeOriginal`)
  - **Format préféré** : `.heic` en priorité
  - **Taille du fichier** (on garde le plus lourd)
  - **Richesse des métadonnées EXIF**
- 📋 **Mode paires uniquement** – ne traiter que les groupes de 2 fichiers (ex. JPG+HEIC) ; ignorer les séries de 3+ pour sélection manuelle
- 🔄 **Transfert de métadonnées** – transférer albums, tags et localisation/EXIF des fichiers supprimés vers le gardé (augmentation uniquement)
- 🧪 **Mode simulation** pour tester sans supprimer, utile pour voir les logs
- ✔️ **Mode confirmation** – validation interactive par groupe [O/n] ; avec DRY_RUN=true seulement log, avec DRY_RUN=false traitement immédiat
- 🗑️ Option de suppression dans la corbeille ou définitive
- 📄 Journalisation automatique dans un fichier `.log` (optionnelle)

---

## ⚙️ Pré-requis

- Serveur Immich opérationnel (auto-hébergé ou public)
- Une **clé API** valide
- Python ≥ 3.7

### Clé API et droits requis

Créez une clé API dans Immich via **Paramètres → Clés API**. La clé doit posséder les droits suivants :

| Droit | Rôle |
|-------|------|
| `duplicate.read` | Récupérer les groupes de doublons |
| `asset.delete` | Supprimer les assets en doublon |
| `asset.update` | Transférer localisation/EXIF (si `IMMICH_TRANSFER_METADATA=true`) |
| `album.read` | Lire les appartenances aux albums pour le transfert |
| `album.asset.create` | Ajouter le gardé aux albums (si `IMMICH_TRANSFER_METADATA=true`) |
| `album.asset.delete` | Retirer le gardé des albums (si `IMMICH_KEEP_METADATA=false`) |
| `tag.asset` | Ajouter/retirer des tags pour le transfert |

Une clé API avec accès complet inclut tous ces droits. Si vous utilisez uniquement `IMMICH_TRANSFER_METADATA=false` et `IMMICH_KEEP_METADATA=true`, les droits minimaux requis sont `duplicate.read` et `asset.delete`.

---

## 📦 Installation

1. Clonez le dépôt et entrez dans le répertoire
2. Créez un environnement virtuel :
   ```bash
   python -m venv .venv
   ```
3. Activez-le (PowerShell sur Windows) :
   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```
   Sur Linux/macOS :
   ```bash
   source .venv/bin/activate
   ```
4. Installez les dépendances :
   ```bash
   pip install -r requirements.txt
   ```

---

## ⚙️ Configuration

1. Copiez le fichier d'exemple et modifiez-le :
   ```bash
   copy .env.example .env   # Windows
   cp .env.example .env     # Linux/macOS
   ```
2. Éditez `.env` et configurez au minimum :
   - `IMMICH_SERVER` – l'URL de votre serveur Immich (ex. `https://immich.example.com`)
   - `IMMICH_API_KEY` – votre clé API (Paramètres → Clés API)
   - `IMMICH_DRY_RUN` – `true` pour simuler uniquement (par défaut), `false` pour supprimer réellement
   - `IMMICH_DEFINITELY` – `false` pour la corbeille (par défaut), `true` pour suppression définitive
   - `IMMICH_ENABLE_LOG` – `true` ou `false` pour la création du fichier log
   - `IMMICH_ONLY_PAIRS` – `false` (défaut) pour traiter tous les groupes, `true` pour n'accepter que les paires de 2 fichiers
   - `IMMICH_HEIC_ONLY` – `false` (défaut) ; `true` pour ne traiter que les groupes contenant au moins un fichier `.heic` ou suffixé `_HEVC`
   - `IMMICH_FILENAME_MATCH` – `false` (défaut) ; `true` pour ne traiter que les groupes dont les noms de base normalisés correspondent tous (ex. `IMG_0127.JPG` avec `IMG_0127-1.JPG`)
   - `IMMICH_KEEP_METADATA` – `true` (défaut) pour que le gardé conserve ses métadonnées, `false` pour les retirer avant transfert
   - `IMMICH_TRANSFER_METADATA` – `true` (défaut) pour transférer albums, tags et localisation des supprimés vers le gardé
   - `IMMICH_CONFIRM` – `true` pour demander [O/n] avant chaque groupe ; `false` (défaut) pour tout ajouter à la liste en bloc
   - `IMMICH_REQUEST_TIMEOUT` – délai en secondes pour les requêtes API (défaut : 5) ; augmenter pour serveurs lents
   - `IMMICH_DELETE_BATCH_SIZE` – taille des lots pour la suppression en bloc (défaut : 500) ; limite la taille du payload par requête

Vous pouvez aussi définir ces variables d'environnement directement, sans fichier `.env`.

---

## 🚀 Utilisation

```bash
python immich_duplicates_fr.py
```

Pour la version anglaise :

```bash
python immich_duplicates_en.py
```

**Conseil :** Exécutez d'abord avec `IMMICH_DRY_RUN=true` (par défaut) pour voir ce qui serait supprimé. Utilisez `IMMICH_CONFIRM=true` pour valider chaque groupe [O/n] ; avec `DRY_RUN=true` seulement log, avec `DRY_RUN=false` traitement immédiat.
