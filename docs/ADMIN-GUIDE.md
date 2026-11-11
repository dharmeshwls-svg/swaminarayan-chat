# Admin guide

The admin page runs on its own port (http://localhost:8001) and only answers on the library computer.
Unlock it with the **admin token** (printed at the end of setup; stored as `admin_token` in the data
folder). **Lock** forgets the token in this browser tab. Every admin action is in the audit trail.

## Books

* **Upload** PDF, EPUB, Word (.docx), text, Markdown, web pages, and scans/photos (OCR in Indian
  languages), or copy files into the books folder (read within 30 seconds; **Scan books folder now**
  checks immediately).
* Status shows *queued*, *processing n%*, *ready* or *failed* (with the reason). **Read again**
  re-reads a book; **Remove** takes it out of the library (the file in the books folder is kept).
* **Download: Allow** lets readers download that book as a signed PDF (off by default).
* **Translate…** opens the Translations tab for that book.

## Book sources

* **Folders on this computer**: the main books folder plus any folders you add. With *Google Drive for
  desktop* installed, add your Drive books folder (Mac: `~/Library/CloudStorage/GoogleDrive-<you>/My Drive/Books`
  with *Mirror files* chosen; Windows: `G:\My Drive\Books`). Removing a folder keeps its books in the library.
* **Google Drive folders (online)**, for any computer including Ubuntu servers:
  1. In console.cloud.google.com create a project and enable the **Google Drive API**.
  2. Create a **service account** (no roles needed), then *Keys > Add key > JSON*; upload the file here.
  3. In Drive, **share** each books folder with the service account's e-mail address as **Viewer**.
  4. Paste the folder link and choose **Add Drive folder**.

  Sub-folders and folder shortcuts are included; PDF, EPUB, Word, text, web pages, images and Google Docs
  (exported as Word) are read. Each folder is checked again every 30 minutes (`DRIVE_SYNC_MINUTES`) or with
  **Check now**; only new or changed files are downloaded. Files removed from Drive are reported and stay in
  the library. The library only reads Drive. For folders shared as *Anyone with the link*, an **API key**
  is enough instead of a service account.

## Unanswered

Questions the books could not answer, with the reason. **Teach answer** saves an approved answer that
is given for that question from then on; **Mark resolved** clears it. Download as CSV.

## Reader corrections

Corrections readers wrote after *Not helpful*. **Approve** makes it an approved answer; **Reject** discards it.

## Translations

* **Translate a book**: choose the book and language. It runs in the background (pause, resume,
  cancel; continues after a restart; waits by itself when the free AI is busy).
* **Review**: original and translation side by side, 50 passages at a time. **Save correction**
  stores your wording and remembers it for future translations. Passages marked *needs checking* failed
  the automatic quality check; a book cannot be published until they are corrected.
* **Preview PDF**, **Publish** (readers can download it), **Unpublish**, **Add to library for
  questions** (the translation becomes a searchable book).
* **Reader suggestions**: edit if needed, then approve (remembered) or reject.
* **Terminology**: names and terms that must always be translated the same way. Sampradaya names
  (Shriji Maharaj, Vachanamrut, Vadtal, Gadhada, Akshardham…) are built in for Gujarati and Hindi.
* The line at the top says which engine translates Gujarati (AI provider or the offline IndicTrans2)
  and shows any problem with it.

## Downloads & terms

* **Signature**: name, title, organisation, note, optional signature image; **Download a sample PDF**.
* **Terms and conditions for AI translation** in English, Gujarati and Hindi. Saving raises the version
  and every reader accepts again.

## Learning

How the library learns without retraining the AI: ratings re-rank passages for similar questions,
rephrased questions teach new words (approve or reject), and answers several readers found helpful can
become approved answers. **Download approved answers (JSONL)**.

## Visitors

Activity grouped by network address (today, 7, 30, 90 days, 1 year): last seen, actions, questions,
translations, downloads, readers, busiest minute, and **flags** for unusual use (many requests in a
minute, slowed down by the rate limit, refused access, many errors).

* Click an address: activity by day, what it did, browsers, latest records; download its records or
  open them in the audit trail.
* **Label** an address ("Temple office"); **Block…** for 1 hour, 24 hours, 7 days or until unblocked
  (this computer cannot be blocked). One address can be a whole office or mobile network.

## Audit trail

Every question, answer, translation, download, correction and admin action, with time, address and
browser. Search by address, action (or a group such as `translate…`), word, date range, or errors only;
**Load older records**; **Download these records (CSV)**. **Check it has not been changed** verifies the
tamper-evident chain (each record carries the fingerprint of the one before it).

## Log files

Sizes of all logs, audit record count and oldest record, download any log, recent warnings and
errors, and **Apply the retention period now**.

## System

**Name and symbol shown to readers** (default *Swaminarayan Sampradaya* and ॐ): change them here; they
appear in the page title and header, beside every answer, in downloaded chats and on PDFs. Readers see the
change after reloading. **Back to the default** returns to `APP_NAME` / `APP_MARK` from the settings file.


Version, books, passages, search index, AI providers and their daily use, background load (worker
threads, AI requests running and waiting), and audit settings.
