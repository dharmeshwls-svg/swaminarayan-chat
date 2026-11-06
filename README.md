# Swaminarayan Sampradaya library chat (internal name: Granth) — chat with your books (Indian languages, text + voice, free to run)

Point it at a **folder of books** and ask questions by typing or speaking. Answers come **only from your books**
(never from the internet), every claim carries a **citation with page number**, and anything it cannot answer is
**logged** so you can add books or improve answers.

* Books: PDF (text or scanned), photos/scans (PNG/JPG/TIFF…), EPUB, DOCX, TXT, HTML, and old DOC/RTF/ODT
* Languages: Gujarati, Hindi, Marathi, Sanskrit, Tamil, Telugu, Kannada, Malayalam, Bengali, Punjabi, Odia, Urdu, English
* Voice: speak your question; hear the answer in a **male voice** (never a female voice); hands-free mode; screen-reader friendly
* Cost: ₹0 — free AI tiers (Groq / Gemini / NVIDIA / Cerebras / OpenRouter / Mistral) with automatic failover, or fully offline with Ollama

## Quick start

Each operating system has its own package; full steps are in [docs/INSTALL.md](docs/INSTALL.md).

| System | Package | Run |
|---|---|---|
| macOS 12+ | `granth-mac-<version>.zip` | `chmod +x granth_setup.sh && ./granth_setup.sh` |
| Windows 10 / 11 | `granth-windows-<version>.zip` | double-click `granth_setup.cmd` |
| Ubuntu 22.04+ / Debian 12 | `granth-ubuntu-<version>.zip` | `chmod +x granth_setup.sh && ./granth_setup.sh` |

Books go in `~/GranthBooks` (Windows: `%USERPROFILE%\GranthBooks`). Open http://localhost:8000; the admin
page is http://localhost:8001 (this computer only). The same installer later does
`status | health | logs | open | admin | restart | stop | ai | update | rollback | version | indic | uninstall`.

**Docker**: `cp .env.example .env`, put books in `./books`, `docker compose up -d --build`.
**Public website**: keep `HOST=127.0.0.1`, put a reverse proxy with HTTPS in front (browsers only allow the
microphone on HTTPS), set `ALLOWED_HOSTS`, `TRUST_PROXY=1`, and an `ACCESS_CODE`.

### Documentation

| Document | For |
|---|---|
| [INSTALL](docs/INSTALL.md) | installing, updating and removing on macOS, Windows and Ubuntu |
| [USER-GUIDE](docs/USER-GUIDE.md) | readers: asking, references, voice, translation, downloads |
| [ADMIN-GUIDE](docs/ADMIN-GUIDE.md) | every tab of the admin page |
| [RUNBOOK](docs/RUNBOOK.md) | daily/weekly routine, backup and restore, updates, incidents |
| [TROUBLESHOOTING](docs/TROUBLESHOOTING.md) | messages and symptoms, with fixes |
| [MONITORING](docs/MONITORING.md) | health checks, logs, automatic restarts, what to watch |
| [CONFIGURATION](docs/CONFIGURATION.md) | every setting in the settings file |
| [COMPATIBILITY](docs/COMPATIBILITY.md) | browsers, phones and tablets |

### Free AI key (2 minutes)
1. https://console.groq.com → create API key → paste as `GROQ_API_KEY=` in `.env`
2. Optional second key for failover: https://aistudio.google.com → `GEMINI_API_KEY=`
3. Check: `python -m granth check-providers`

Without any key the app still works in **passages-only mode**: it shows the best matching passages with citations.
Free-tier limits and model names change often — `check-providers` tells you what works today.

## Using it
The interface works like a modern chat app, on a computer or a phone.

* **Ask** in the box at the bottom (it sits in the middle of the screen when a chat is new). `Enter` asks, `Shift+Enter` adds a line.
  Under the box: **All books** opens the library so you can pick which books to search, and the **globe** sets the answer language (Auto answers in the language you asked in).
* **Chats** are in the left sidebar, grouped by day. Hover a chat to rename it, download it as text, or delete it. The sidebar icon hides or shows the sidebar.
* **Citations** appear as small numbered chips in the answer. Clicking one opens that passage below the answer; **Show surrounding text** shows the neighbouring paragraphs. Only the first two sources are shown until you choose **Show more sources**.
* **Badges** under each answer: *Checked against the books* means every citation and quotation was found in the passages. *Not found in the books* means it refused rather than guessed; the closest passages are folded underneath. *Answer approved by the librarian* is a taught answer. *General knowledge* appears only if `CHAT_MODE` is hybrid or general.
* **Under each answer:** Read aloud (male voice), Copy, Translate, Helpful, Not helpful. Not helpful opens a small box where the reader can type the right answer; it waits for admin approval.
* **Voice:** the microphone in the box, or `Alt+M`. **Hands-free** (top right, or `Alt+V`) listens, answers aloud, then listens again; say "stop" to end.
  `Alt+S` stop speaking, `Alt+R` repeat answer, `Alt+/` question box, `Alt+N` new chat.
* **Settings** (sidebar): speaking speed, read answers automatically, Light / Dark / High contrast, text size. Dark follows the computer's setting until you choose.
* **Admin** is not on the reader page. It has its own page on its own port, **http://localhost:8001**, reachable only from this computer (see *Admin page* below).
* The page loads no outside fonts or scripts, so it works fully offline on your internal network.

## Questions about the library, summaries, and spelling
* **Which books are there?** Ask in any words or language, typos included: "which books do you have", "list the bokks in the libary", "પુસ્તકાલયમાં કયા પુસ્તકો છે?", "पुस्तकालय में कौन सी किताबें हैं?". The answer lists every ready book.
* **Short summary of each book:** add "summary" (or સાર / सारांश): "tell what are books in library and give a short summary". Each book's summary appears as soon as it is written (first 12 books; ask for others by title). **One book:** "summary of the Gita", "what is Vachanamrut about".
* Summaries are written by the AI from passages taken from the beginning, middle and end of the book, using only those passages. They are saved, so the next request is instant and costs nothing, and a book is summarised again only if its file changes. Without an AI writer, the opening lines of the book are shown instead, labelled as such.
* **Spelling correction:** misspelt words are corrected to the closest words that actually appear in your books ("rigth to perfrom" becomes "right to perform"), and the answer says "Searched for: …" so the reader can see what was searched. Words that are not close to anything in the books are left as typed.
* **Name shown to readers:** `APP_NAME` (default *Swaminarayan Sampradaya*) and `APP_MARK` (default ॐ) in `~/granth/app/.env`; restart after changing. The program, commands and folders keep the internal name `granth` so updates and uninstall keep working.

## References, translation and reading aloud
* **Every source shows where it is**, so a reader can open the book and find it:
  * PDF and scanned books: **page and line** on that printed page.
  * Text files: the **line number** in the file, the same as in any text editor.
  * Word, EPUB and web pages: the **paragraph number**.
  * A **verse number** written in the passage, such as `[3.6]`, `।।1.1।।` or `॥ २.४७ ॥`, is shown as "verse 2.47".
  * **Copy reference** copies, for example, "Bhagavad Gita, page 12, line 8, verse 2.47". Downloaded chats and the chat log use the same format.
  * **Show surrounding text** shows the neighbouring passages with their line numbers.
* **Books added before version 2.3** have no line numbers yet. Admin, then Books, then **Add line numbers to older books** re-reads them. Each book is unavailable for searching for the minute or two this takes.
* **Translate** is on every answer, every source passage and every translation. It offers all supported languages and remembers your last choice. The result is labelled as a translation, and the original passage remains the reference. It needs an AI writer (a free key, or Ollama for fully local use).
* **Read aloud** is on every answer, source passage and translation. **Only male voices are used.** The server's male neural voice is used first. If that can't be reached, the device's own voice is used only if it is a known male voice. Otherwise nothing is read and the reader is told that a male voice is not available. A female or unknown voice is never used.

## Answers and references
Answers start with a short summary (2–4 sentences, then at most a few bullets) and cite the passages they use. Below each answer, **References** lists exactly the passages the answer cites, with book, page, line or paragraph, and verse. **Other passages searched** holds the rest, folded.

## Which books a question covers
* With **no books selected**, every question covers the whole library.
* With **books selected** (Library, or the book button under the question box), questions cover only those books. "Summary of each book" summarises just the selected books and says "Your selected books (2 of 5)", plus a hint on how to include every book.
* If the question **mentions the library or all books**, it covers the whole library even when books are selected, and the answer says so. Examples: "library", "all books", "every book", "पुस्तकालय", "सभी पुस्तकें", "બધાં પુસ્તકો", "ગ્રંથાલય", "सगळी पुस्तके". "Each book" alone keeps your selection.
* **While a translation is running**, the question box, microphone and send button are disabled and show "Translating into …". They unlock when the translation arrives, or if it fails.

## Admin page (its own port)
* Readers use **http://localhost:8000**. The admin page is at **http://localhost:8001**, or run `~/granth/granth_setup.sh admin`. It needs the admin token from `~/granth/data/admin_token`.
* The reader port has **no admin functions at all**: no Admin button, and admin addresses return "not found" there. That covers uploads, removing or re-reading books, folder scans, the unanswered queue, corrections, learning, the audit trail and system status. Attempts with a token on the reader port are refused, and refused admin attempts are recorded in the audit trail.
* The admin page listens on `127.0.0.1` only, so other computers cannot reach it even if the reader port is later opened through a reverse proxy. `serve` refuses to start the admin page on a network address unless `ALLOW_LAN=1` is set on purpose. Don't do that for admin; use an SSH tunnel instead: `ssh -L 8001:localhost:8001 you@server`.
* Settings: `ADMIN_PORT` (default 8001, `0` = no admin page) and `ADMIN_HOST` (default `127.0.0.1`). Both ports run in the same service and share one library.
* The admin page also lists every book, with **Read again** and **Remove**. Removing takes a book out of the library; the file in your books folder is not deleted.

## Downloading books (signed PDF)

Downloads are off for every book until the admin allows them (admin page, Books tab, "Allow").
A reader then sees "Download PDF" for that book in the Library.

* Every download is a PDF with a cover page carrying the admin's signature (name, title,
  organisation, a note and an optional signature image, set on the "Downloads & terms" tab),
  the date, and a unique copy code. The copy code is also in the footer of every page and in
  the audit trail (action `download`), so a copy found elsewhere can be traced to the download.
* PDF books keep their original pages, with the footer stamped on. Word, EPUB, text and web
  books are typeset with bundled Noto fonts, so Gujarati, Devanagari, Tamil and other scripts
  are shaped correctly. Scanned books keep their page images.
* "Download a sample PDF" on the admin page shows exactly what readers will get.
* This is a visible signature, not a cryptographic one. A certificate-based PDF signature
  (for example with pyHanko and a certificate from a certifying authority) can be added later
  if you need tamper-proof copies.

## Translating a whole book

On the Translations tab, choose a book and a language and start. The book is translated in
passages in the background; you can pause, resume or cancel, and it survives restarts. When the
free AI is busy (rate limits), the job waits and continues by itself.

1. **Review**: each passage shows the original next to the translation. Correct any passage
   and save. 
2. **Preview PDF**: see the translated book as readers will get it.
3. **Publish**: readers can then download "<language> translation (AI), PDF" for books whose
   download is allowed. Unpublish at any time.
4. **Add to library for questions** (optional): the translation becomes a book of its own,
   titled "… (AI translation)", so readers can ask questions in that language.

## Better translation into Gujarati and other Indian languages

Small free chat models (for example Llama on Groq) translate Gujarati poorly: names come out
misspelt ("સાહજાનાની" for Sahajanand, "વાદતલ" for Vadtal), English words are written in Gujarati
letters ("બુક"), the "(en)" label gets translated, and sometimes one word repeats in a loop.
The library now guards against all of these:

* **Fixed names.** Sampradaya names, places and scriptures (Shriji Maharaj, Sahajanand Swami,
  Vachanamrut, Vadtal, Ahmedabad, Gadhada, Akshardham…) are replaced before translation and put
  back with the correct Gujarati or Hindi spelling (`granth/glossaries/translation_names.tsv`;
  your own Terminology on the admin page takes precedence).
* **Protected markers.** Citations [1], verse numbers [3.6], language labels (en) and links are
  never translated.
* **Quality check.** Every result is checked for repeated words, wrong script, added notes,
  lost names and text that is too long or short. A failed result is retried with another
  model; if all fail, the reader is told it could not be translated reliably instead of
  being shown a bad translation. In a whole-book translation the passage is marked
  "needs checking" and the book cannot be published until you correct it.
* **Best model first.** Translations, summaries and answers in Indian languages go first to
  the providers that handle them well (`TRANSLATE_ORDER`, Gemini first). **A free Gemini key
  is the simplest big improvement** (https://aistudio.google.com, add it with
  `./granth_setup.sh ai`).
* **Offline option: IndicTrans2.** AI4Bharat's IndicTrans2 (IIT Madras, MIT licence) is a
  dedicated translation model for English and 22 Indian languages. It runs on the Mac, free,
  with no daily limit. Install with `./granth_setup.sh indic` (about 1 GB of packages and 3 GB of
  models, downloaded during setup so no reader waits for them). It gets its own Python environment (`~/granth/venv-indic`)
  because it needs transformers 4.x while meaning-based search needs 5.x; the app talks to it
  through a small worker process. Remove it, with its models, with `./granth_setup.sh indic remove`.
  AI4Bharat shares the models only with signed-in Hugging Face users who accept their
  conditions (free). `./granth_setup.sh indic` checks this first and, if needed, explains the
  three steps (free account, "Agree and access repository" on each of the three model pages, a
  "Read" token) and asks for the token, which is saved as `HF_TOKEN` in the settings file. Until
  access is granted, nothing large is downloaded and translation keeps using the AI. With `TRANSLATE_ENGINE=auto` it is used when no Gemini, OpenAI,
  Mistral or DeepSeek key is set; `TRANSLATE_ENGINE=indictrans2` always uses it, with the AI
  as a fallback when its result fails the checks.
* **Speed.** Long text is split into pieces that are translated at the same time; a translation
  asked for again comes from a 30-day cache (cleared when you approve a correction); Gemini's
  slow "thinking" step is turned off (`GEMINI_REASONING`); a reader waiting on the Translate
  button gets at most two tries per piece. The offline translator loads its model when the app
  starts, not when the first reader asks. The audit trail records how long each translation took
  (`ms`).
* **Without a Gemini key.** Translation uses IndicTrans2 when it is installed (no key, no limit).
  It is tuned for speed: the whole text goes to the model in one request with all its sentences
  batched, the reader's Translate button uses the fastest search (`beams=1`) while whole-book
  translations use a more careful one (`beams=4`), and on Apple Silicon the app measures whether
  the CPU or the GPU is faster on your Mac and uses that. While the model is still loading after
  a restart, readers get a clear "still loading, try again shortly" message instead of waiting.
  Without IndicTrans2 and without a strong AI key, translation uses whatever AI you set up (for
  example Groq), with the quality checks above.
* **It keeps learning.** Corrections you save and reader suggestions you approve are reused
  exactly next time and given to the model as examples.

## Terms and conditions for AI translation

Before a reader translates anything, or downloads a translated book, a pop-up shows the terms
and asks them to tick "I understand…" and accept. The wording (English, Gujarati, Hindi) is
edited on the "Downloads & terms" tab, like the signature. The terms are also printed on the
cover of every translated PDF. Saving new wording raises the version number, and every reader is
asked to accept again. The server checks it too: a translation request without the current
accepted version is refused. Each acceptance is recorded in the audit trail (`terms_accepted`).

## How translations get better (learning without retraining)

* **Translation memory**: every passage you correct, and every reader suggestion you approve,
  is stored. The same passage is then reused exactly (no AI call), and similar passages are
  given to the AI as examples to follow.
* **Terminology**: names, places and scripture titles that must always be translated the same
  way (for example "Akshardham → अक्षरधाम"). They are given to the AI with every translation.
* **Reader suggestions**: under every translation readers see "Suggest a better translation".
  Suggestions wait on the Translations tab; you can edit, approve or reject them. Nothing a
  reader suggests is used until you approve it.

## Learning from readers
The AI model is **not** retrained. Free hosted models cannot be fine-tuned, and a model that memorises answers stops reading them from the books. Instead, the search and answer layer learns. This is cheap and reversible, and every answer stays grounded in the books.

* **Ratings re-rank search.** A helpful / not-helpful rating is stored against the passages the answer cited and the words of the question. For similar later questions, passages that readers agreed were helpful go to the top, even if the wording would have missed them. Unhelpful ones sink.
  * A rating acts only once `LEARN_MIN_READERS` (default 2) different readers agree. Each reader counts once, and their latest vote wins.
  * The effect is capped, and chosen-book filters are respected.
  * Ratings follow their passage when a book is re-read, and are dropped if its text changed.
* **Rephrased questions teach words.** If a question finds nothing and the same reader's rephrased question, within 5 minutes, is answered, the unknown word and the word that worked are suggested as a pair, for example *farz → duty*. Approving it in **Admin → Learning** adds it to `data/glossary.txt`. With `LEARN_AUTO_APPROVE=3`, it is added automatically once 3 readers needed it.
* **Helpful answers become approved answers.** Questions that several readers rated helpful appear in **Admin → Learning**. **Make approved answer** gives that answer to everyone who asks it.
* **Overview:** questions this week, answered, not found, helpful / not helpful, passages re-ranked, words learned.
* **Export:** **Download approved answers (JSONL)** gives the approved and rated-helpful question/answer pairs, in case you later fine-tune a local model (for example with Ollama). That is optional, and not needed for the learning above.
* Turn learning off with `LEARN_FROM_FEEDBACK=0`. Every learning action is in the audit trail.
* **Spelling correction** now leaves words of 4 letters or fewer alone. They are too ambiguous ("farz" is a real word, not a typo of "far").

## Visitors and log management (admin page)

* **Visitors** groups everything in the audit trail by network address: when it was first and
  last seen, how many questions, translations, downloads and readers, and its busiest minute.
  Unusual use is flagged: many requests in one minute, being slowed down by the rate limit,
  refused access, or many errors. Click an address for its activity by day, what it did, its
  browsers and its latest records; download them as CSV or open them in the audit trail.
* **Label** an address ("Temple office") so you recognise it everywhere, or **block** it for
  1 hour, 24 hours, 7 days or until you unblock it. A blocked address gets a short "blocked"
  message; this computer's own address can never be blocked. Every label, block and unblock is
  in the audit trail. Note that one address can be a whole office or mobile network.
* **Audit trail** can be searched by address, action (or a group such as `translate…`), any
  word in the record, a date range, or errors only; older records load page by page, and
  "Download these records" exports exactly what the filters show.
* **Log files** lists every log with its size (app log, daily audit files, unanswered
  questions, chat history), lets you download them, shows recent warnings and errors, and
  applies the retention period on demand.
* Refusals (rate limit, blocked) are recorded at most once a minute per address, so a flood of
  requests cannot flood the log. With `AUDIT_IP_MODE=hash` or `truncate`, grouping and blocking
  work on the coded or shortened address.

## Working on several things at once

* Requests run on up to `THREADS` (64) worker threads, so readers do not wait for each other.
* AI requests are limited to `LLM_CONCURRENCY` (8) at a time; the rest wait in a short queue
  (a reader is told "busy" only if it is still full after a minute), which keeps everyone within
  the free providers' rate limits.
* Audit records are written by a background writer that keeps the hash chain in order, so a
  question never waits for the log.
* Library summaries are written four books at a time; whole-book translations translate
  `TRANSLATE_WORKERS` passages at a time; `INGEST_WORKERS` books are read and indexed at once.
* Search reads a consistent snapshot of the meaning index, and a very large library being added
  to reloads it at most every 20 seconds instead of after every batch.

## Audit trail for a live service
* **Every action is recorded:** chat, feedback, correction, translation, admin action, and refused admin attempt. Each record holds the time, IP address, browser, visitor id and session. Chat records also have the question, answer, the references cited, which AI answered, and how long it took.
* **Tamper-evident:** each record stores a fingerprint (SHA-256) of itself plus the previous record's fingerprint. Changing, inserting or removing a record, including the newest, is detected by **Admin → Audit trail → Check it has not been changed**, or by `python -m granth audit-verify`.
* **Daily files** in `data/logs/audit/audit-YYYY-MM-DD.jsonl` carry the same fingerprints. Copy them to another machine or to write-once storage (S3 Object Lock, Azure immutable blobs, GCS retention lock) and check that copy with `python -m granth audit-verify --files /path/to/copy`. Someone with full control of this computer could rewrite the whole chain, so an off-machine copy is what makes the trail trustworthy.
* **Retention:** `AUDIT_RETENTION_DAYS` (default 365) and `SESSION_RETENTION_DAYS` (default 90) are applied every 6 hours. Removing old records keeps the chain checkable. DPDP Rules 2025, Rule 8(3), ask for processing logs to be kept for at least one year.
* **IP privacy:** `AUDIT_IP_MODE=full | truncate | hash`.
* Application logs (`data/logs/granth.log`) are kept separately from the audit trail and rotated at 5 MB.

## Updating and removing (macOS single file)
* **Update:** download the newer `granth_setup.sh` and run it, or run `./granth_setup.sh update`. It stops Granth, saves the current version, backs up the library database to `~/granth/data/backups` (the 2 newest copies are kept; `SKIP_BACKUP=1` skips this for very large libraries), installs the new version and starts it again. Your settings and keys, admin token, chats, index, audit trail and books are kept, and it does not ask for the AI again.
* **Undo an update:** `~/granth/granth_setup.sh rollback` goes back to the version you had before the last update and keeps your data.
* **Which version:** `~/granth/granth_setup.sh version` shows the installed version, the version inside the file, and what rollback would restore.
* **Remove:** `~/granth/granth_setup.sh uninstall` stops and removes the service and the app. It asks before deleting chats and the index; if you keep them, your settings and keys are kept too and come back if you install again. `uninstall -y` removes everything in `~/granth` without asking. Add `--with-ollama` to also remove Ollama and its models.
* Your books folder is never touched. Homebrew tools installed along the way (Python 3.12, Tesseract, Poppler) are left in place because other apps may use them; remove them with `brew uninstall` if you want.


Removing:

* `./granth_setup.sh indic remove`: only the offline translator (its environment and models).
* `./granth_setup.sh uninstall`: the app and its environments; asks before deleting chats, index,
  models and logs, Ollama, the Homebrew tools for scanned books, and pip's download cache.
* `./granth_setup.sh uninstall --all`: everything above without asking. Your books folder is never
  touched; Homebrew's Python 3.12 is kept because other apps may use it.

## What it does for accuracy
Hybrid search (keyword with Indian-script-aware tokenizing + optional meaning-based embeddings) → fusion → diversity →
confidence gate (refuses instead of guessing) → LLM answers from numbered passages only → citation/quote verifier →
unanswered log. Details: `docs/IMPLEMENTATION_PLAN.md`; measured results and honest gaps: `docs/TEST_REPORT.md`.

## Files
`granth/` app · `tests/` · `eval/` retrieval benchmark on real text · `scripts/` installers (`setup_template.sh` for macOS/Ubuntu, `windows/` for Windows) · `docs/` · `.env.example`
Develop: `pip install -r requirements-dev.txt && python -m pytest`

## Teaching the chatbot (no model training needed)
1. Drop documents into `~/GranthBooks` (any format/language). They are indexed automatically.
2. Questions it could not answer appear in **Admin → Unanswered**. Click **Teach answer** to store a curated answer;
   it is used from then on for that question (any wording/language), with your source label.
3. Add synonyms in other languages to `data/glossary.txt` (one concept per line, `term | term | ...`).
4. Greetings/“which books?” are answered locally; abusive or spam input gets a calm reply and never reaches the AI.
Sample check: `python eval/run_loya_eval.py` (21/22 on the Loya sample; the miss is an off-topic question sharing one common word, which the AI writer then refuses).

## Folder input, learning from readers, and audit trail
- **Folder input:** set `IMPORT_DIR` (default `~/GranthBooks`). Every supported file in it, including subfolders, is indexed; changed files are re-indexed and unchanged ones are skipped. Files modified in the last 3 seconds are picked up on the next scan so half-copied files are not indexed.
- **Learning:** when a reader gives a thumbs-down they can type the right answer. It goes into **Admin → Corrections from readers**. Approving it creates a curated answer that is used from then on. Nothing a reader types is used before an admin approves it.
- **Audit trail:** every chat, feedback, correction, admin action and denied admin attempt is written to the `audit` table and `data/logs/audit.jsonl`, with time, IP address, browser (User-Agent), owner cookie, session, question, answer, sources and any traceback. Export it from **Admin → Download audit CSV** or `GET /api/admin/audit?fmt=csv`.
- **Privacy:** IP addresses and browsers are personal data. Tell your users, restrict access to the admin token, and set a retention policy. Behind a reverse proxy set `TRUST_PROXY=1` so the real client IP is logged; otherwise the proxy's address is logged.

## Network safety (stays on this computer)
- Granth listens on **127.0.0.1 only** by default. Other devices on your Wi-Fi or the internet cannot reach it.
- `serve` refuses any other address unless you set `ALLOW_LAN=1` on purpose. Docker publishes the port to `127.0.0.1` only (see `docker-compose.yml`).
- Ollama is pinned to `127.0.0.1:11434`.
- Forwarded client-IP headers are trusted only from a local reverse proxy (`127.0.0.1`), so visitors cannot fake their IP in the audit log.
- Check what is listening: `~/granth/granth_setup.sh ports`. It warns if anything is reachable from other computers.
- Keep your firewall on (macOS: System Settings → Network → Firewall).
- For a public website, put a TLS reverse proxy (Caddy) in front and set `ALLOWED_HOSTS` and `TRUST_PROXY=1`. Only then open the port to the internet.

## Gujarati and Sanskrit
- Spelling variants are folded: Gujarati and Devanagari digits, nukta, candrabindu, avagraha, zero-width joiners, and IAST (saṃsāra → samsara).
- Sanskrit question words (इति, भवति, किम् …) and Gujarati particles are ignored when searching.
- The glossary links Sanskrit and romanised terms across scripts: dharma/धर्म/ધર્મ, karma/कर्म/કર્મ, yoga, samadhi, tapas, brahman, ishvara, vachanamrut/વચનામૃત, granth, shastra.
- Prefix matching finds compound words: a question for धर्म finds धर्मक्षेत्रे.
- OCR uses Tesseract's Gujarati and Sanskrit models (`tesseract-lang`, installed by the Mac installer).
- **Limits:** Sanskrit sandhi (words joined across boundaries) is only partly handled, so very long compounds may need a glossary line. Sanskrit and Gujarati translations of terms were written by hand and should be reviewed by a native speaker.

## Voice and translation
- **Male voice only.** Read-aloud always uses a male neural voice (Edge voices: Prabhat for English, Madhur for Hindi, Niranjan for Gujarati, Manohar for Marathi, Valluvar for Tamil, Mohan for Telugu, Gagan for Kannada, Midhun for Malayalam, Bashkar for Bengali, Salman for Urdu). Female voices were removed on purpose; the voice selector is gone.
- If no neural voice can be reached, the browser's own male voice is used, or a lower pitch on the default voice.
- Sanskrit and Punjabi/Odia have no dedicated Edge voice here; they use the male Hindi voice, which reads Devanagari (Sanskrit) and Gurmukhi-free text poorly for Punjabi. Check with `python -m granth check-voices`.
- **Translation.** Every answer has a 🌐 Translate button. It translates into the language chosen in *Answer language* (English when set to auto). The model is instructed to translate only: names, numbers, `[n]` citations and verse numbers are kept, and nothing is added. Translation needs an AI provider. A free key (Groq, Gemini) works, and so does local Ollama, which keeps translation fully on this computer.
- Translations are shown as a labelled block under the answer. They are not new sources; the original cited passage remains the reference.
