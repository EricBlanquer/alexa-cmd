# alexa-cmd

Send text commands to Alexa devices from the command line, using Amazon's web API.

Authentication is automatic: cookies are read live from your Firefox profile.
When the session expires, Firefox is opened to the Amazon login page and the
tool polls until you finish logging in — no copy/paste step.

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

## How auth works

- Cookies are read from `~/.mozilla/firefox/<profile>/cookies.sqlite` (the
  install-default profile from `profiles.ini`).
- Only unpartitioned cookies on `*amazon.fr` are used.
- On HTTP 401/302, Firefox is reopened to the login URL and the tool polls
  every 2s for up to 5 minutes.

## Configuration

Stored in `~/.config/alexa-cmd/`:

| File | Content |
|------|---------|
| `devices.json` | Cached device list |
| `config.json` | Default device + customer ID |

## Requirements

- Python 3
- `requests` library
- Firefox (with at least one Amazon login already done in the profile)

## Limitations

- Amazon's Alexa web API is unofficial and undocumented.
- The Alexa web SPA (alexa.amazon.fr) has been deprecated by Amazon, but the
  API endpoints still work.
- `customerId` is required in the payload (extracted automatically from the
  device list).
- Auto-login requires Firefox; other browsers are not supported.
