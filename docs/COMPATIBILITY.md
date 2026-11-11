# Browsers, phones and tablets

## Supported browsers

| Browser | Minimum | Chat, references, library, translation, downloads | Voice input (microphone) | Reading aloud (male voice) |
|---|---|---|---|---|
| Chrome / Edge (Windows, macOS, Linux, Android) | 108 | yes | yes | yes |
| Safari (macOS, iPhone, iPad) | 15.4 (iOS/iPadOS 15.4) | yes | yes | yes; on iPhone/iPad after a tap |
| Firefox (Windows, macOS, Linux, Android) | 115 | yes | no (Firefox has no voice input: type, or install server speech recognition) | yes |
| Samsung Internet | 21 | yes | yes | yes |
| Older iPads (iPadOS before 15.4) | — | works with reduced styling (dialog fallback built in) | depends | after a tap |

Reading aloud uses the library's own male voices (from the voice service, cached on the library
computer). If that service cannot be reached, the browser's own **male** voice is used; if the device
has none, nothing is read aloud and the reader is told (never a female voice).

## Tested

| What | How | Result |
|---|---|---|
| Phones: iPhone SE (320×568), iPhone 13, iPhone 15 Pro Max, Pixel 7, Galaxy S9+ | Chromium with each device's screen size, pixel density, touch and user agent | no sideways scrolling; question box, menus, sidebar, Library and Settings dialogs fully on screen; buttons at least 38–44 px for fingers |
| Tablets: iPad Mini, iPad (7th gen), iPad Pro 11 landscape, Galaxy Tab S4 | same | same |
| Desktop 1280–1920 px | Chromium | same |
| Admin page on all of the above | every tab | no sideways scrolling (wide tables scroll inside their box) |
| Full flows (ask, cite, read aloud, translate with terms, suggest, stop, download, admin tabs) | 47 automated browser tests (jsdom) + 200+ server tests | all pass |
| Firefox and Safari engines | checked feature by feature against what those browsers support (fallbacks below) | not run in the real browsers in this build environment |

## Fallbacks built in

| Feature | Newer browsers | Fallback |
|---|---|---|
| Full-height layout | `100dvh` | `100vh` |
| Highlight of selected books and current chat | `:has()` | a class set by the page |
| Colour mixing | `color-mix()` | fixed colours |
| Dialogs (Library, Settings, terms) | `<dialog>` | built-in stand-in for older Safari |
| Copy | Clipboard API (needs HTTPS or this computer) | classic copy, so it also works over plain HTTP on a local network |
| Hover-only buttons (chat rename, download, delete) | hover | always visible on touch screens |
| Sound on phones/tablets | autoplay after an answer | "tap Read aloud" message when the device blocks sound |
| Browser auto-translation (Chrome) | turned off for the app, each message marked with its real language | the app's own Translate button |

## Notes for phones and tablets

* By default only the library computer opens the library. To use phones and tablets, see
  *Reaching it from other devices* in [INSTALL.md](INSTALL.md); HTTPS through a reverse proxy is best
  (the microphone needs HTTPS on phones).
* Add to Home Screen works on iPhone, iPad and Android (opens full screen with the library's colour).
