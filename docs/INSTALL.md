# Installation

The Swaminarayan Sampradaya library chat (internal name **Granth**) runs on one computer and is
used from a web browser on that computer. It is free to run: books stay on the computer, and the
AI that writes answers is either free (Ollama on the computer, or a free online key) or optional.

| | macOS | Windows | Ubuntu / Debian |
|---|---|---|---|
| Package | `granth-mac-<version>.zip` | `granth-windows-<version>.zip` | `granth-ubuntu-<version>.zip` |
| Installer | `granth_setup.sh` | `granth_setup.cmd` (runs `granth_setup.ps1`) | `granth_setup.sh` |
| Supported | macOS 12 or newer, Apple Silicon or Intel | Windows 10 (1809+) and 11 | Ubuntu 22.04 / 24.04, Debian 12 |
| Installs to | `~/granth` | `%LOCALAPPDATA%\Granth` | `~/granth` |
| Books folder | `~/GranthBooks` | `%USERPROFILE%\GranthBooks` | `~/GranthBooks` |
| Runs as | launchd agent `com.granth.app` | scheduled task "Granth library" | systemd user service `granth.service` |

## What you need

* **Memory:** 4 GB minimum; 8 GB recommended. 16 GB or more if you use Ollama (AI on the computer)
  or the offline Indian-language translator.
* **Disk:** about 3 GB for the app with meaning-based search, plus your books, plus about 5 GB for
  Ollama models or 4 GB for the offline translator if you use them.
* **Internet:** for installation, and for the online AI if you choose that. Readers use the library
  through a browser on the same computer.
* **An AI for writing answers** (choose during setup, change later with the `ai` command):
  * **Free online key** (recommended for Gujarati and Hindi: Google Gemini, https://aistudio.google.com).
    Groq, Cerebras, NVIDIA, OpenRouter and Mistral also have free keys.
  * **Ollama**: runs on the computer, no key, works offline, needs 8 GB+ memory.
  * **Skip**: readers then see the matching passages with page and line numbers, without a written answer.

## macOS

1. Install Homebrew if you do not have it: https://brew.sh
2. Unzip `granth-mac-<version>.zip`, open **Terminal** in that folder and run:

        chmod +x granth_setup.sh
        ./granth_setup.sh

3. Setup installs Python 3.12, Tesseract (with Indian languages) and Poppler with Homebrew, creates
    the app's Python environment, asks which AI to use, and starts the background service. It opens
    the library in your browser when it is ready and prints the **admin token**.
4. Put books in `~/GranthBooks`. Keep this folder **outside** Documents, Desktop and Downloads:
    macOS does not let background apps read those folders.

## Windows 10 and 11

1. Unzip `granth-windows-<version>.zip` (right-click > Extract All). Do not run it from inside the zip.
2. Double-click **`granth_setup.cmd`**. If Windows SmartScreen warns, choose *More info > Run anyway*.
3. Setup uses **winget** (built into Windows 10 1809+ and 11) to install, for your user only where possible:
    * Python 3.12, if no Python 3.10+ is found;
    * Tesseract OCR and Poppler (Windows may ask for permission), and downloads the Indian-language
     OCR data into the app's data folder.
4. It creates the app's Python environment in `%LOCALAPPDATA%\Granth`, asks which AI to use, creates
    the scheduled task **"Granth library"** (starts at log-on, restarts if it stops; if a task cannot
    be created it uses a Startup-folder shortcut instead), starts it and opens the library.
5. Put books in `%USERPROFILE%\GranthBooks`.

Later commands are run from `%LOCALAPPDATA%\Granth\granth_setup.cmd`, for example
`granth_setup.cmd status` in a Command Prompt.

No winget? Install Python 3.12 from https://www.python.org/downloads/windows/ (tick *Add python.exe to
PATH*) and Tesseract from https://github.com/UB-Mannheim/tesseract/wiki, then run setup again.

## Ubuntu / Debian

1. Unzip `granth-ubuntu-<version>.zip` and in a terminal in that folder run:

        chmod +x granth_setup.sh
        ./granth_setup.sh

2. Setup asks for your password once (sudo) to install `python3-venv`, `poppler-utils` and
    `tesseract-ocr` with the Indian-language packs through apt. It then works as your user in `~/granth`.
3. It installs a **systemd user service** (`~/.config/systemd/user/granth.service`) and enables
    *lingering* so the library starts at boot even before you log in. Where there is no systemd user
    session (some containers and WSL), it runs as a background process and adds an `@reboot` entry
    to your crontab.
4. Put books in `~/GranthBooks`.

Run setup as your normal user, not with `sudo`.

## Options for setup

Set these before running setup (macOS/Linux: `PORT=8080 ./granth_setup.sh`; Windows Command Prompt:
`set PORT=8080` then `granth_setup.cmd`):

| Option | Default | Meaning |
|---|---|---|
| `AI` | ask | `ollama`, `key` or `skip` |
| `BOOKS_DIR` | `~/GranthBooks` | folder that is watched for new books |
| `PORT` / `ADMIN_PORT` | `8000` / `8001` | library chat / admin page ports |
| `WITH_SEMANTIC` | `1` | `0` skips meaning-based search (saves ~1.5 GB; keyword search still works) |
| `WITH_INDIC` | `0` | `1` also installs the offline Indian-language translator |
| `OLLAMA_MODEL` | by memory | e.g. `qwen2.5:7b` |
| `PYTHON_BIN` | found automatically | a specific Python 3.10+ |
| `SKIP_BACKUP` | `0` | `1` skips the database copy made before an update |

## After installation

* Library chat: http://localhost:8000. Admin page: http://localhost:8001, **this computer only**.
  Unlock it with the admin token (shown at the end of setup, and stored in the data folder as `admin_token`).
* Add books: copy them into the books folder (read automatically within 30 seconds), or upload them on
  the admin page (Books tab).
* Check everything: `granth_setup status` (see [MONITORING.md](MONITORING.md)).

## Optional: offline Indian-language translation (IndicTrans2)

`granth_setup indic` installs AI4Bharat's IndicTrans2 in its own Python environment and downloads its
models (~3 GB). The models are shared only with signed-in Hugging Face users who accept AI4Bharat's
conditions (free): setup lists the three model pages, asks for a Hugging Face **Read** token, checks
access, and only then downloads. Remove it again with `granth_setup indic remove`. A free Gemini key
gives good Gujarati translation without it.

## Updating

Download the new package and run its installer the same way. Settings, keys, chats, index and the
audit trail are kept; the library database is copied to `data/backups` first, and the previous
version is kept for `granth_setup rollback`. `granth_setup version` shows the installed version and
the version in the package.

## Uninstalling

| Command | Removes |
|---|---|
| `granth_setup indic remove` | only the offline translator, its environment and models |
| `granth_setup uninstall` | the app, its environments and the service; asks before deleting chats, index, models and logs, Ollama, the OCR tools, and pip's download cache |
| `granth_setup uninstall --all` | all of the above without asking |

Your books folder is never deleted. Python itself is kept, because other programs may use it.

(`granth_setup` means `./granth_setup.sh` on macOS/Ubuntu, or `granth_setup.cmd` on Windows.)

## Reaching it from other devices (phones, tablets) — optional

By default only the computer itself can open the library. To let phones and tablets on the same
network use it, a reverse proxy with HTTPS on the computer is recommended (Caddy, nginx). The quick
local-network way is `HOST=0.0.0.0` and `ALLOW_LAN=1` in the settings file, then restart; set an
`ACCESS_CODE` so only people who know the code can chat. The admin page always stays on this
computer only. See [CONFIGURATION.md](CONFIGURATION.md).
