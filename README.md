# alexa-cmd

Send text commands to Alexa devices from the command line, using Amazon's web API.

Authentication is automatic: the default web browser is detected
(`xdg-settings get default-web-browser`), its session cookies are read live
from disk, and on missing/expired session that browser is opened to the
Amazon login page. The tool polls until you finish logging in — no copy/paste
step.

## Usage

```bash
# Send a text command (as if you spoke to Alexa) — no subcommand needed
alexa allume la lumiere du salon
alexa eteint la lumiere
alexa quelle heure est-il
alexa mets de la musique

# Target a specific device (partial name match)
alexa -d echo eteins la lumiere
alexa -d freebox mets france inter

# Text-to-speech (Alexa speaks the text)
alexa say bonjour tout le monde

# Device management
alexa devices          # list devices (* = default)
alexa default echo     # change default device
alexa setup            # force re-auth + show devices
```

The first time you run any command, a Firefox tab opens on
`https://alexa.amazon.fr/api/devices-v2/device`. Log in to Amazon — the tool
detects the fresh cookies automatically and continues.

## Browser support

| Browser | Source |
|---------|--------|
| Firefox | `~/.mozilla/firefox/<profile>/cookies.sqlite` (unpartitioned cookies only) |
| Chrome / Chromium / Brave | `~/.config/<browser>/Default/Cookies` (AES-128-CBC + PBKDF2-HMAC-SHA1, password from libsecret `Chrome Safe Storage`); on cookies DB version ≥ 24 the 32-byte SHA-256 host integrity prefix is stripped |

If the detected default browser cannot be read (decryption fails or no
cookies), the tool falls back to the next available browser. Polls every 2s
for up to 5 minutes during auto-login.

## Configuration

Stored in `~/.config/alexa-cmd/`:

| File | Content |
|------|---------|
| `devices.json` | Cached device list |
| `config.json` | Default device + customer ID |

## Requirements

- Python 3
- `requests` library
- One of: Firefox, Chrome, Chromium, Brave
- `xdg-settings`, `xdg-utils` (for default-browser detection)
- `cryptography` and `secret-tool` (only needed if using Chromium-family)

## Limitations

- Amazon's Alexa web API is unofficial and undocumented.
- The Alexa web SPA (alexa.amazon.fr) has been deprecated by Amazon, but the
  API endpoints still work.
- `customerId` is required in the payload (extracted automatically from the
  device list).
- Chromium has a newer `v12` scheme (AES-256-GCM with a key derived via the
  XDG Secret Portal) that is bound to the Chrome process and cannot be
  decrypted by other processes. Cookies encrypted as `v12` are skipped.
  Tested on Chrome 148: cookies are still written as `v11` (libsecret), so
  decryption works.
