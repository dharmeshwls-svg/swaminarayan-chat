# Configuration

Settings live in the **settings file** `.env` in the app folder (`~/granth/app/.env`, Windows
`%LOCALAPPDATA%\Granth\app\.env`). One `NAME=value` per line. Restart after a change
(`granth_setup restart`). Setup manages the server and folder settings for you; the file also holds AI
keys, so keep it private. `.env.example` in the package lists everything with comments.

## AI providers (answers, summaries, translation)

| Setting | Default | Meaning |
|---|---|---|
| `GEMINI_API_KEY`, `GROQ_API_KEY`, `CEREBRAS_API_KEY`, `NVIDIA_API_KEY`, `OPENROUTER_API_KEY`, `MISTRAL_API_KEY`, `DEEPSEEK_API_KEY`, `OPENAI_API_KEY` | empty | keys; any one is enough, several give automatic failover |
| `<NAME>_MODEL`, `<NAME>_BASE_URL`, `<NAME>_RPM`, `<NAME>_RPD` | built in | model, address, requests per minute and per day for a provider |
| `OLLAMA_MODEL`, `OLLAMA_URL` | empty, `http://127.0.0.1:11434` | AI on this computer |
| `CUSTOM_BASE_URL`, `CUSTOM_MODEL`, `CUSTOM_API_KEY` | empty | any OpenAI-compatible server |
| `LLM_ORDER` | `groq,gemini,cerebras,nvidia,openrouter,deepseek,openai,mistral,ollama,custom` | which provider is tried first for English |
| `TRANSLATE_ORDER` | `gemini,openai,mistral,deepseek,openrouter,cerebras,nvidia,groq,ollama,custom` | order for Indian languages (answers, summaries, translations) |
| `GEMINI_REASONING` | `none` | Gemini Flash's slow "thinking" step; `default` keeps Google's setting |
| `GLOBAL_LLM_DAILY_BUDGET` | `0` (no limit) | stop using the AI after this many requests a day |
| `CHAT_MODE` | `strict` | `strict` answers only from the books; `hybrid` also gives general answers (labelled) when the books have nothing |

## Translation

| Setting | Default | Meaning |
|---|---|---|
| `TRANSLATE_ENGINE` | `auto` | `auto`: a strong AI key if set, else IndicTrans2 if installed; `llm`; `indictrans2` |
| `INDIC_PYTHON` | set by `granth_setup indic` | Python of the offline translator's own environment |
| `HF_TOKEN` | set by `granth_setup indic` | Hugging Face Read token for the IndicTrans2 models |
| `INDICTRANS_DEVICE` | `auto` | `cpu`, `mps` (Apple GPU), `cuda`; `auto` measures which is faster |
| `TRANSLATE_WORKERS` | `2` | book passages translated at the same time |

## Server and network

| Setting | Default | Meaning |
|---|---|---|
| `HOST`, `PORT` | `127.0.0.1`, `8000` | library chat address and port |
| `ADMIN_HOST`, `ADMIN_PORT` | `127.0.0.1`, `8001` | admin page (`ADMIN_PORT=0` turns it off) |
| `ALLOW_LAN` | `0` | must be `1` to listen on anything other than this computer |
| `ACCESS_CODE` | empty | readers must enter this code before chatting |
| `ALLOWED_HOSTS` | empty | domain names allowed behind a reverse proxy |
| `TRUST_PROXY` | `0` | trust `X-Forwarded-For` from a local reverse proxy (for correct visitor addresses) |
| `CHAT_RATE_PER_MIN`, `MEDIA_RATE_PER_MIN` | `25`, `40` | per-address limits for questions and for voice/translation |
| `THREADS` | `64` | requests handled at the same time |
| `LLM_CONCURRENCY` | `8` | AI requests at the same time; others wait in a short queue |

## Books and search

| Setting | Default | Meaning |
|---|---|---|
| `IMPORT_DIR` | books folder | folder watched for new books |
| `SCAN_INTERVAL` | `30` | seconds between folder checks |
| `INGEST_WORKERS` | `2` | books read at the same time |
| `DRIVE_SYNC_MINUTES` | `30` | how often connected Google Drive folders are checked (folders and keys are set on the admin page) |
| `MAX_UPLOAD_MB`, `MAX_PDF_PAGES` | `300`, `5000` | limits per book |
| `OCR_MODE`, `OCR_DPI` | `auto`, `200` | OCR for scans: `auto` (when a page has little or poor text), `force` (every page), `off` |
| `EMBED_BACKEND`, `EMBED_MODEL` | `auto`, `intfloat/multilingual-e5-small` | meaning-based search (`none` = keywords only) |
| `TOP_K`, `CHUNK_CHARS`, `CONTEXT_BUDGET` | `6`, `500`, `3800` | passages per answer, passage size, text given to the AI |
| `CACHE_TTL_HOURS` | `168` | reuse an identical recent answer |

## Voice

| Setting | Default | Meaning |
|---|---|---|
| `TTS_BACKEND` | `auto` | male voices from the online voice service, cached; the browser's male voice as fallback |
| `STT_MODEL` | `small` | server speech recognition (optional `requirements-voice.txt`) for browsers without voice input |

## Learning, privacy and audit

| Setting | Default | Meaning |
|---|---|---|
| `LEARN_FROM_FEEDBACK` | `1` | ratings re-rank passages; rephrasings teach words |
| `LEARN_MIN_READERS`, `LEARN_AUTO_APPROVE` | `2`, `3` | readers needed before a rating counts; before a learned word is added without review (`0` = admin only) |
| `AUDIT_RETENTION_DAYS` | `365` | keep audit records this long (`0` = forever); DPDP Rules 2025 ask for at least one year |
| `AUDIT_IP_MODE` | `full` | `full`, `truncate` (network only) or `hash` (a code per visitor) |
| `SESSION_RETENTION_DAYS` | `90` | delete chats older than this |
| `TRANSCRIPT_LOG` | `1` | readable transcript file |
| `APP_NAME`, `APP_MARK` | `Swaminarayan Sampradaya`, `ॐ` | default name and symbol shown to readers (the admin page System tab overrides them) |
| `LOG_LEVEL` | `INFO` | `DEBUG` for more detail |
