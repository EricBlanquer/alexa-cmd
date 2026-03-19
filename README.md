# alexa-cmd

Send text commands to Alexa devices from the command line, using Amazon's web API.

## Setup

```bash
alexa setup
```

1. Open `https://alexa.amazon.fr/api/devices-v2/device` in your browser (you'll be redirected to login)
2. After login, you'll see JSON data — the API works
3. Open DevTools (F12) > Network tab
4. Refresh the page (F5)
5. Click on the `device` request
6. Copy the full `Cookie` header value from Request Headers
7. Paste it when prompted

Cookies expire every few weeks — re-run `alexa setup` when you get a 401 error.

## Usage

```bash
# Send a text command (as if you spoke to Alexa)
alexa send allume la lumiere du salon
alexa send quelle heure est-il
alexa send mets de la musique

# Target a specific device (partial name match)
alexa send -d echo eteins la lumiere
alexa send -d freebox mets france inter

# Text-to-speech (Alexa speaks the text)
alexa say bonjour tout le monde

# Device management
alexa devices          # list devices (* = default)
alexa default echo     # change default device
```

## Configuration

Config files are stored in `~/.config/alexa-cmd/`:

| File | Content |
|------|---------|
| `cookies.txt` | Amazon session cookies (chmod 600) |
| `devices.json` | Cached device list |
| `config.json` | Default device + customer ID |

## Requirements

- Python 3
- `requests` library

## Limitations

- Amazon's Alexa web API is unofficial and undocumented
- Cookies expire every few weeks
- The Alexa web SPA (alexa.amazon.fr) has been deprecated by Amazon, but the API endpoints still work
- `customerId` is required in the payload (extracted automatically during setup/device discovery)
