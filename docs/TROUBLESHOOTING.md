# Troubleshooting

Start with `granth_setup status` and `granth_setup logs` (`granth_setup` = `~/granth/granth_setup.sh` on
macOS/Ubuntu, `%LOCALAPPDATA%\Granth\granth_setup.cmd` on Windows). The admin page > **Log files** shows
recent warnings and errors.

## Installation

| Message or symptom | Cause | Fix |
|---|---|---|
| `Homebrew is required` (macOS) | Homebrew missing | install from https://brew.sh, run setup again |
| `Python 3.12 not found` (macOS) | brew could not install it | `brew install python@3.12` |
| `Python's venv module is missing` (Ubuntu) | `python3-venv` not installed | `sudo apt install python3-venv` |
| `Python 3.10 or newer is needed` (Ubuntu 20.04) | old Python | upgrade to Ubuntu 22.04+, or `PYTHON_BIN=/path/to/python3.12 ./granth_setup.sh` |
| Windows: "Windows protected your PC" | SmartScreen on a downloaded script | *More info > Run anyway* |
| Windows: `Python was installed but cannot be found yet` | PATH not refreshed | close the window, open a new one, run `granth_setup.cmd` again |
| Windows: runs `python` from the Microsoft Store | Store placeholder | setup ignores it; install Python from python.org or let setup use winget |
| `pip install failed` | no internet, proxy, or disk full | check internet and free space; run setup again |
| `ERROR: pip's dependency resolver ... conflicts` | packages from an older version | `granth_setup repair` |
| `the service did not answer within 90 s` | app failed to start | read the log lines shown; see "Service" below |
| `the app folder is missing next to this script` (Windows) | ran from inside the zip | extract the whole zip first |

## Service

| Symptom | Fix |
|---|---|
| `NOT ANSWERING` on port 8000 | `granth_setup restart`; then `granth_setup logs` |
| `address already in use` in the log | another program uses the port: `granth_setup ports`; choose others with `PORT`/`ADMIN_PORT` and run setup again |
| macOS: `launchd refused to start the service` | `launchctl bootout gui/$(id -u)/com.granth.app` then `granth_setup start` |
| macOS: books not read, `Operation not permitted` | books folder is inside Documents/Desktop/Downloads: move it (e.g. `~/GranthBooks`) or give Python *Full Disk Access* |
| Ubuntu: stops when you log out | `sudo loginctl enable-linger $USER` |
| Ubuntu: `no systemd user session` warning | normal in containers/WSL: it runs as a background process; `granth_setup start` after a reboot if cron is not available |
| Windows: does not start after log-on | Task Scheduler > "Granth library" > History; or `granth_setup.cmd start`; corporate PCs may block tasks: setup then uses a Startup-folder shortcut |
| Windows: a console window flashes | update to the latest package (OCR steps run without windows) |

## Admin page

| Symptom | Fix |
|---|---|
| "That admin token is not correct" | copy it again from `data/admin_token` (no spaces) |
| Admin page does not open from another device | by design: it answers only on the library computer |
| Admin page opens the chat instead | use port 8001 (`ADMIN_PORT`), not 8000 |

## Books

| Symptom | Cause | Fix |
|---|---|---|
| Book *failed*: "no text found" | a scan without OCR tools | install Tesseract and Poppler (setup does), then **Read again** |
| Gujarati scan reads as nonsense | OCR language missing | macOS: `brew install tesseract-lang`; Ubuntu: `sudo apt install tesseract-ocr-guj`; Windows: run setup again (downloads language data) |
| Book stays *queued* | another large book is being read | wait; two books are read at once (`INGEST_WORKERS`) |
| "Already in the library" | same file content | nothing to do |

## Answers

| Symptom | Cause | Fix |
|---|---|---|
| Only passages, no written answer | no AI configured | `granth_setup ai` |
| "The AI is busy with other readers" | many questions at once | wait a moment; raise `LLM_CONCURRENCY` if the provider allows |
| Answers in poor Gujarati | small free model (e.g. Llama on Groq) | add a free **Gemini** key (`granth_setup ai`) |
| "Not found in the books" for something that is there | different words | rephrase; approve learned words on **Learning**; or **Teach answer** |
| Answers slow | free provider rate limits, or Ollama on a small computer | add a second key; use a smaller Ollama model |

## Translation

| Message or symptom | Fix |
|---|---|
| "Please read and accept the translation terms first" | accept the terms in the pop-up (they changed) |
| "could not produce a reliable translation" | the AI's result failed the quality check; try later, or add a Gemini key |
| `Cannot access gated repo` / `401` (offline translator) | sign in to Hugging Face, accept the conditions on the three AI4Bharat model pages, then `granth_setup indic` and paste a Read token |
| `'NoneType' object has no attribute 'shape'` (offline translator) | newer `transformers` than AI4Bharat's model code supports: run `granth_setup indic` from version 2.11.0 or later (it installs 4.46; checked against their model code). Older packages (e.g. a `granth-2-19` folder) still have the bug |
| "The offline translator is still loading" | first start after a restart loads the model; wait a minute |
| Windows: offline translator install fails building a package | install *Microsoft C++ Build Tools* (`winget install Microsoft.VisualStudio.2022.BuildTools`), then `granth_setup.cmd indic` |
| Chrome offers to translate the page and the text changes | the app tells browsers not to; choose *Show original* once, or update to the latest version |

## Voice

| Symptom | Fix |
|---|---|
| "No male voice available" | the voice service needs internet; or install a male system voice (Windows: Settings > Time & language > Speech; macOS: Accessibility > Spoken Content) |
| Microphone does nothing (Firefox) | Firefox has no voice input; use Chrome, Edge or Safari, or type |
| Phone/tablet does not read aloud automatically | the device allows sound only after a tap: tap **Read aloud** |
| "Microphone permission was denied" | allow the microphone for this site in the browser settings |

## Hands-free

| Symptom | Fix |
|---|---|
| Stops listening after a moment of silence | update to 2.12.0 or later (it now keeps listening) |
| Does not listen again after an answer | update to 2.12.0 or later; check that *Read aloud* works on its own |
| "Tap the microphone to keep talking" | the browser needs a tap to listen again (Safari after the screen locked): tap the microphone |
| "Hands-free paused after a long silence" | press Hands-free again |
| Hears its own voice | use headphones, or lower the speaker volume |
| Firefox | no voice input in Firefox: use Chrome, Edge or Safari |

## Google Drive

| Message | Fix |
|---|---|
| "folder not found, or not shared with the library" | share the Drive folder with the service-account e-mail shown on the Book sources tab, as Viewer |
| "Google did not accept the service-account key" | upload a fresh JSON key; the old one may have been deleted in Google Cloud |
| "Google refused access (403) … Drive API has not been used" | enable the **Google Drive API** in the same Google Cloud project |
| "Google Drive is not connected" | upload the service-account key, or save an API key (public folders only) |
| A folder from Google Drive for desktop is "not found" | Google Drive for desktop must be running; on a Mac choose *Mirror files*, not *Stream files* |
| Google Sheets / Slides not added | only Google Docs are read (exported as Word); save others as PDF in Drive |

## Phones and tablets

| Symptom | Fix |
|---|---|
| Cannot open the library from a phone | by default only the library computer can; see *Reaching it from other devices* in [INSTALL.md](INSTALL.md) |
| Copy does not work over the local network | update to the latest version (copy works without HTTPS now) |

## Collecting information for support

`granth_setup status`, `granth_setup version`, the last 100 lines of `data/logs/granth.log` and
`data/logs/service.err.log`, and the browser and device used. Remove API keys before sharing anything
from `.env`.
