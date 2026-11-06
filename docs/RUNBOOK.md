# Runbook

Day-to-day operation of the library computer. `granth_setup` below means `~/granth/granth_setup.sh`
on macOS and Ubuntu, or `%LOCALAPPDATA%\Granth\granth_setup.cmd` on Windows.

## Where things are

| | macOS / Ubuntu | Windows |
|---|---|---|
| App | `~/granth/app` | `%LOCALAPPDATA%\Granth\app` |
| Settings and keys | `~/granth/app/.env` | `%LOCALAPPDATA%\Granth\app\.env` |
| Data (database, index, audio cache, models) | `~/granth/data` | `%LOCALAPPDATA%\Granth\data` |
| Logs | `~/granth/data/logs` | `%LOCALAPPDATA%\Granth\data\logs` |
| Database backups (made before each update) | `~/granth/data/backups` | `...\data\backups` |
| Admin token | `~/granth/data/admin_token` | `...\data\admin_token` |
| Books folder | `~/GranthBooks` | `%USERPROFILE%\GranthBooks` |
| Service | macOS: launchd `com.granth.app`; Ubuntu: `systemctl --user ... granth` | Task Scheduler: "Granth library" |

## Commands

| Command | Use |
|---|---|
| `granth_setup status` | service state, both ports, Ollama, disk, error count |
| `granth_setup health` | `OK`/`FAIL` with exit code 0/1, for monitors |
| `granth_setup start` / `stop` / `restart` | control the background service |
| `granth_setup logs` | follow the app log |
| `granth_setup open` / `admin` | open the library / admin page |
| `granth_setup ports` | what is listening, and a warning if anything is reachable from other computers |
| `granth_setup ai` | change the AI (Ollama or a key) and restart |
| `granth_setup version` / `update` / `rollback` | versions, update from this package, go back |
| `granth_setup repair` | fix Python package conflicts |
| `granth_setup indic` / `indic remove` | add or remove the offline translator |
| `granth_setup uninstall [--all]` | remove the app |

## Routine

**Daily (2 minutes)**
1. `granth_setup status`: both ports answer, error count not rising.
2. Admin page > **Unanswered**: teach answers to repeated questions; **Reader corrections**; reader
    suggestions on **Translations**.

**Weekly**
1. Admin > **Visitors**: look at flagged addresses; label known ones; block abuse with a time limit.
2. Admin > **Log files**: recent warnings and errors.
3. Admin > **Audit trail** > **Check it has not been changed**.
4. Copy the daily audit files (`data/logs/audit/audit-*.jsonl`) to another computer or drive. A copy
    elsewhere is what makes the trail trustworthy.

**Monthly**
1. Back up (below) and test that the backup opens.
2. Admin > **System**: AI providers' daily use against their free limits.
3. Install updates if a new package is available.

## Adding and changing books

* Copy files into the books folder, or upload on the admin page. A file whose content changed is read
  as a new book; remove the older copy on the Books tab.
* Large scanned books take time (OCR). Progress shows on the Books tab; two books are read at once
  (`INGEST_WORKERS`).
* To remove: admin > Books > **Remove**. Removing the file from the books folder does not remove it
  from the library.

## Backup and restore

What to back up: the **data folder** (library database, index, audit files, settings copy), the
**settings file** `app/.env` (contains keys: keep it private), and the **books folder**.

1. `granth_setup stop`
2. Copy the data folder, `app/.env` and the books folder to the backup drive.
3. `granth_setup start`

Restore on the same or a new computer: install the same version, `granth_setup stop`, copy the data
folder and `.env` back into place, `granth_setup start`. Restoring only the database: copy a file from
`data/backups/library-<date>.db` over `data/library.db` (and delete `library.db-wal` and
`library.db-shm`) while the service is stopped.

## Updating

1. Download the new package for your system and unzip it.
2. Run its installer. It stops the service, keeps the previous version for rollback, backs up the
    database, updates, repairs packages and starts again.
3. Check `granth_setup status`, ask one question, open the admin page.
4. Problem? `granth_setup rollback` returns to the previous version (settings, chats and index kept).

## Changing the AI

`granth_setup ai`. For Gujarati and Hindi a **Gemini** key gives the best results. Several keys can be
set at once in `.env`; they are used in turn when one reaches its free limit (`LLM_ORDER`).
Check keys: `cd <app folder> && ../venv/bin/python -m granth check-providers` (Windows:
`..\venv\Scripts\python -m granth check-providers`).

## Rotating the admin token

Stop the service, delete `data/admin_token`, start the service: a new token is created; read it from
the same file. Everyone using the admin page must unlock again.

## Moving to a new computer

Install on the new computer, stop it, copy the data folder, `.env` and books folder from the old one
(see Backup and restore), start it, then uninstall from the old computer.

## Incident: the library is down

1. `granth_setup status` and `granth_setup health`.
2. `granth_setup restart`.
3. Still down: `granth_setup logs`, and the end of `data/logs/service.err.log`. Match the message in
    [TROUBLESHOOTING.md](TROUBLESHOOTING.md).
4. After an update: `granth_setup rollback`.
5. Database damaged: restore from `data/backups` (above).

## Incident: abuse or unusual traffic

1. Admin > **Visitors**: sort out the flagged address; open it to see what it did.
2. **Block…** for 24 hours (or longer); the reason goes into the audit trail.
3. If the library is reachable from the internet, set `ACCESS_CODE` and lower `CHAT_RATE_PER_MIN`.

## Incident: wrong or harmful answer reported

1. Find it: Admin > **Audit trail**, search a word from the question.
2. **Teach answer** (Unanswered) or approve the reader's correction, so the right answer is given from then on.
3. A wrong translation: correct it on **Translations** (it is remembered).
