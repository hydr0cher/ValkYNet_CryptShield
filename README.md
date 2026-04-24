<div align="center">

<br/>

<img src="https://img.shields.io/badge/⬡-ValkYNet-1D6FE8?style=for-the-badge&labelColor=0E1219" alt="ValkYNet"/>

# ValkYNet CryptShield

### Enterprise File Encryption Suite for Windows

<br/>

[![Release](https://img.shields.io/github/v/release/hydr0cher/valkynet-cryptshield?style=flat-square&color=1D6FE8&label=Latest%20Release)](https://github.com/hydr0cher/valkynet-cryptshield/releases/latest)
[![Platform](https://img.shields.io/badge/Platform-Windows%2010%20%2F%2011-0078D4?style=flat-square&logo=windows)](https://github.com/hydr0cher/valkynet-cryptshield/releases/latest)
[![Cipher](https://img.shields.io/badge/Cipher-AES--256--GCM-00C896?style=flat-square)](https://en.wikipedia.org/wiki/Galois/Counter_Mode)
[![No Install](https://img.shields.io/badge/No%20Install-Portable%20.exe-F0A030?style=flat-square)](#download)
[![License](https://img.shields.io/badge/License-MIT-blueviolet?style=flat-square)](LICENSE)

<br/>

**A portable, offline-first desktop application for encrypting and decrypting files using AES-256-GCM authenticated encryption — no installation, no cloud, no tracking.**

<br/>

---

</div>

## Download

> **No Python or dependencies required — download and run.**

| File | OS | Size |
|------|----|------|
| [`ValkYNet_CryptShield.exe`](https://github.com/hydr0cher/valkynet-cryptshield/releases/latest) | Windows 10 / 11 (64-bit) | ~25 MB |

**→ [Go to Releases](https://github.com/hydr0cher/valkynet-cryptshield/releases/latest)**

> **Windows SmartScreen notice:** On first run, Windows may show a blue "Windows protected your PC" dialog because the file isn't commercially code-signed. Click **"More info" → "Run anyway"** to proceed. The application is open-source and contains no malware.

---

## What It Does

ValkYNet CryptShield lets you encrypt any file on your computer using military-grade authenticated encryption. Only someone with the exact same key can decrypt the file — even if they have the encrypted `.vkenc` output, they cannot read it without the key.

It runs entirely locally — no account, no internet connection, no data ever leaves your machine.

---

## Features

### 🔒 Encrypt
- Drag & drop files onto the app or browse with a file picker
- Batch encrypt multiple files at once with a single key
- Choose between **AES-256-GCM** (default) or **ChaCha20-Poly1305** cipher
- Optional **zlib compression** before encryption to reduce output size
- Each file gets a unique random 256-bit salt — encrypting the same file twice gives different output
- Encrypted files saved as `filename.ext.vkenc` alongside the original

### 🔓 Decrypt
- Drop any `.vkenc` file created by CryptShield
- Original filename is preserved and restored on decryption
- Automatically detects the cipher and settings used during encryption
- If a file was tampered with, decryption fails with an authentication error

### 🗝 Key Registry
- Generate cryptographically secure 256-bit random keys
- Save keys with a description and creation timestamp
- Preview keys with masked display (first 8 + last 8 chars visible)
- One-click copy to clipboard
- Email a key securely via the built-in email dialog (SMTP)
- Export your full key registry to a JSON backup file

### 📋 Audit Log
- Every action is logged with a timestamp: logins, encryptions, decryptions, key events, password changes
- Color-coded event viewer (success / warning / danger)
- Stores last 500 events locally — export or clear any time

### ⚙ Settings
- Change the app password (minimum 8 characters, real-time strength meter)
- View system info: active cipher, KDF rounds, crypto library status, local data paths

### 🔐 Login Screen
- Frameless, draggable login dialog with brute-force protection
- 3 failed attempts → progressive timed lockout (30s, 60s, up to 120s)
- All login attempts (success and failure) are audit-logged

---

## Quick Start

1. **Download** `ValkYNet_CryptShield.exe` from the [Releases page](https://github.com/hydr0cher/valkynet-cryptshield/releases/latest)
2. **Run** the `.exe` — no installation needed
3. **Login** with the default password: `ValkYNet2024!`
4. **Change your password** immediately in **Settings → Change Password**
5. **Encrypt a file:** Go to Encrypt → drag your file → generate or enter a key → click Encrypt

---

## How to Encrypt & Decrypt

### Encrypting a file

```
1. Open the Encrypt tab
2. Drag & drop your file(s) into the drop zone, or click to browse
3. Enter a 64-character hex key  OR  type a passphrase (it will be derived to a key via PBKDF2)
4. Click "Generate Key" to create a new random key if you don't have one
5. Save the key — you CANNOT recover it later
6. Click ENCRYPT ALL FILES
7. Output: yourfile.pdf.vkenc saved next to the original
```

### Decrypting a file

```
1. Open the Decrypt tab
2. Drag & drop the .vkenc file(s)
3. Paste the same key used during encryption
4. Click DECRYPT ALL FILES
5. Output: original file restored in the same folder
```

> ⚠️ **There is no key recovery.** If you lose your key, the encrypted file cannot be recovered by anyone — including the developer.

---

## Security Details

| Property | Value |
|----------|-------|
| Primary cipher | AES-256-GCM (authenticated) |
| Alternative cipher | ChaCha20-Poly1305 (authenticated) |
| Key derivation (passphrase) | PBKDF2-SHA256, 310,000 rounds |
| Key derivation (scrypt) | via `cryptography` library |
| Nonce | 12 bytes, random per operation |
| Salt | 32 bytes, random per file |
| Auth tag | 16 bytes (tamper detection) |
| App password storage | SHA-256 hash only — never plaintext |

**Authenticated encryption** means the app detects any modification to an encrypted file. If even one byte is changed, decryption will fail and alert you rather than producing corrupted output silently.

---

## Local Data

All data stays on your computer. Three files are created in your home directory (`C:\Users\<you>\`):

| File | Contents |
|------|----------|
| `.valkynet_config.json` | App password hash |
| `.valkynet_keys.json` | Your saved key registry |
| `.valkynet_audit.json` | Event audit log (last 500 entries) |

None of these files are sent anywhere. You can delete them at any time; the app will recreate them with defaults on next launch.

---

## Encrypted File Format

CryptShield uses a custom binary format for `.vkenc` files:

```
MAGIC (6B) | SALT (32B) | ALGO (24B) | COMPRESS FLAG (1B) | NAME LEN (2B) | ORIG NAME (NB) | NONCE (12B) | CIPHERTEXT + AUTH TAG
```

- `MAGIC` — `VKNET2` — identifies the file as a valid CryptShield v2 archive
- `SALT` — unique per file, used during key derivation
- `ALGO` — which cipher was used (stored so you don't need to remember)
- `COMPRESS FLAG` — whether zlib compression was applied before encryption
- `ORIG NAME` — original filename, restored automatically on decrypt
- `NONCE` — random 12-byte initialization vector for GCM/ChaCha20
- `CIPHERTEXT + AUTH TAG` — encrypted data with 16-byte integrity tag

---

## FAQ

**Q: Can I use this on macOS or Linux?**
The `.exe` is Windows-only. The Python source (`valkynet_crypt.py`) runs on macOS and Linux with `pip install PyQt5 cryptography`.

**Q: Is the encryption the same as 7-Zip / VeraCrypt?**
CryptShield uses AES-256 in GCM mode (authenticated). 7-Zip uses AES-256 in CBC mode (not authenticated by default). VeraCrypt encrypts full volumes. CryptShield is designed for individual file encryption with authentication.

**Q: What happens if I encrypt the same file twice?**
Each encryption uses a new random salt and nonce, so the two outputs will be completely different even with the same key. Both can be decrypted with the same key.

**Q: Can multiple files be encrypted with one key?**
Yes. The Batch mode on both Encrypt and Decrypt tabs handles multiple files in one operation.

**Q: Where does the key go after I close the app?**
Nowhere. Keys exist only in memory while the app is running. They are never written to disk unless you explicitly save them to the Key Registry.

**Q: Antivirus flags the file. Is it safe?**
Yes. False positives on unsigned Python executables are common. The source code is fully open and available in this repository for review.

---

## Building From Source

If you prefer to build the executable yourself rather than trust the pre-built binary:

```bash
# Clone the repo
git clone https://github.com/hydr0cher/valkynet-cryptshield.git
cd valkynet-cryptshield

# Install dependencies
pip install PyQt5 cryptography pyinstaller

# Build
pyinstaller build.spec
# Output: dist/ValkYNet_CryptShield.exe
```

See [`BUILD.md`](BUILD.md) for the full build guide including the `.spec` file.

---

## Roadmap

- [ ] Folder encryption (recursive, zip + encrypt)
- [ ] Argon2id KDF option
- [ ] Secure keystore export (password-protected)
- [ ] `.vkenc` file association (double-click to decrypt)
- [ ] macOS `.app` bundle
- [ ] Auto-wipe original after successful encryption (optional)

---

## Contributing

Issues and pull requests are welcome. Please open an issue before starting work on large features.

---

## License

[MIT License](LICENSE) — free to use, modify, and distribute.

---

<div align="center">

Made by **[hydr0cher](https://github.com/hydr0cher)**

<sub>100% offline · No telemetry · No accounts · Open source</sub>

</div>
