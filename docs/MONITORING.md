# Monitoring and service tracking

## Health checks

| Check | How | Healthy |
|---|---|---|
| Library chat | `GET http://127.0.0.1:8000/healthz` | `{"ok": true, "version": "..."}` (checks the database too) |
| Admin page | `GET http://127.0.0.1:8001/healthz` | same |
| Both, as a command | `granth_setup health` | prints `OK ...`, exit code 0; `FAIL ...`, exit code 1 |
| Everything an operator looks at | `granth_setup status` | service running, both ports answer, errors not rising |
| Details (admin token) | `GET http://127.0.0.1:8001/api/admin/status` with header `X-Admin-Token: <token>` | JSON: books, passages, AI providers (ready, cooling down, daily use), background load, audit writer queue |
| Audit trail intact | admin > Audit trail > *Check it has not been changed*; or in the app folder: `GRANTH_DATA=~/granth/data ../venv/bin/python -m granth audit-verify` (Windows: `set GRANTH_DATA=%LOCALAPPDATA%\Granth\data` then `..\venv\Scripts\python -m granth audit-verify`) | `database: OK`, `daily files: OK` |

`/api/admin/status` → `load` shows `ai.active` and `ai.waiting` (AI requests running and queued),
`worker_threads`, `translation_workers` and `threads_running`; `audit.waiting_to_write` should stay near 0.

## Automatic checks

**macOS / Ubuntu (cron)** — check every 5 minutes, restart if down, keep a record:

```
crontab -e
*/5 * * * * ~/granth/granth_setup.sh health >/dev/null || (~/granth/granth_setup.sh restart; date >> ~/granth/data/logs/restarts.log)
```

**Windows (Task Scheduler)** — create a task that runs every 5 minutes:

```
schtasks /Create /SC MINUTE /MO 5 /TN "Granth health" /TR "cmd /c \"%LOCALAPPDATA%\Granth\granth_setup.cmd health || %LOCALAPPDATA%\Granth\granth_setup.cmd restart\""
```

The service itself already restarts after a crash (launchd *KeepAlive*, systemd *Restart=always*,
Task Scheduler *restart every minute*); these checks also catch a hung process.

**Uptime monitors** (Uptime Kuma, Healthchecks.io agent, Zabbix, Nagios): an HTTP check on
`http://127.0.0.1:8000/healthz` expecting `"ok":true`, run on the same computer.

## Logs

| File (in the data folder's `logs`) | Contents | Kept |
|---|---|---|
| `granth.log` | the app's own log (INFO, WARNING, ERROR) | rotated at 5 MB, 5 copies |
| `service.out.log`, `service.err.log` | what the service printed (start-up errors) | grows; trim when large |
| `audit/audit-YYYY-MM-DD.jsonl` | the audit trail, one file per day, hash-chained | `AUDIT_RETENTION_DAYS` (365) |
| `unanswered.jsonl` | questions the books could not answer | grows |
| `chat_history.txt` | readable transcript (if `TRANSCRIPT_LOG=1`) | grows |

On Ubuntu with systemd also: `journalctl --user -u granth`. On Windows: Task Scheduler history of
"Granth library". All log files can be viewed and downloaded on the admin page > **Log files**.

What to look for in `granth.log`:

| Line | Meaning |
|---|---|
| `Granth x.y.z up: N books, M chunks, embeddings=..., llm=[...]` | a successful start |
| `provider X failed (HTTP 429 ...)` | a free AI key hit its limit; the next provider is used |
| `translation by X rejected (...)` | an AI translation failed the quality check and was retried |
| `offline translator unavailable: ...` | IndicTrans2 not usable; translation uses the AI |
| `ingest failed for book N` | a book could not be read (reason follows) |
| `audit write failed` | investigate: disk full or database locked |

## Activity and usage

* Admin > **Visitors**: who uses the library (by network address), how much, and flags for unusual use.
* Admin > **Audit trail**: every action, searchable and exportable as CSV for reports.
* Admin > **Learning**: questions in the last 7 days, answered, not found, rated helpful.
* Admin > **System**: each AI provider's use today against its daily limit.

## Alerts worth acting on

| Signal | Action |
|---|---|
| `health` fails twice in a row | `granth_setup restart`; read the log |
| Errors in `granth.log` rising | Log files tab; [TROUBLESHOOTING.md](TROUBLESHOOTING.md) |
| Many *Not found in the books* | add books, teach answers, approve learned words |
| A provider always *cooling down* | add another free key, or use Gemini |
| Flagged addresses on Visitors | look, label or block |
| Disk space low (`status`) | trim `service.*.log`, remove old `data/backups`, remove unused Ollama models (`ollama rm`) |
| Audit check not intact | stop and investigate; compare with the copy kept elsewhere |
