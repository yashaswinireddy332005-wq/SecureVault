<<<<<<< HEAD
# 🔐 Secure File Vault

A multi-user file encryption vault with password + TOTP two-factor authentication, available as both a Flask web app and a command-line tool.

![Python](https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white)
![Flask](https://img.shields.io/badge/Flask-000000?style=flat&logo=flask&logoColor=white)
![Cryptography](https://img.shields.io/badge/AES--256-CFB-red?style=flat)
![bcrypt](https://img.shields.io/badge/bcrypt-Password%20Hashing-blue?style=flat)
![TOTP](https://img.shields.io/badge/2FA-TOTP-green?style=flat)

---

## 📌 Overview

Secure File Vault lets each registered user encrypt and store files behind two layers of protection: a **bcrypt-hashed password** and a **TOTP one-time code** (compatible with Google Authenticator and similar apps). Files are encrypted with **AES-256** before ever touching disk, and each user gets an isolated vault folder.

The project ships two interfaces to the same core logic — a polished **Flask web UI** for everyday use, and a **CLI** for quick local encrypt/decrypt operations.

---

## ✨ Features

- ✅ **Two-factor authentication** — bcrypt password hash + TOTP (QR code enrollment via `pyotp` + `qrcode`)
- ✅ **AES-256 file encryption** (CFB mode, random IV per file)
- ✅ **Multi-user support** — each user gets an isolated vault directory
- ✅ **Web dashboard** — upload, encrypt, decrypt, and download files from the browser
- ✅ **CLI mode** — encrypt/decrypt files directly from the terminal
- ✅ **In-place local encryption** — encrypt a file on disk while also backing it up to your vault
- ✅ **Activity logging** — registrations, logins, and encryption events are timestamped and logged

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python | Core language |
| Flask | Web framework & routing |
| `cryptography` | AES-256 encryption/decryption |
| `bcrypt` | Password hashing |
| `pyotp` | TOTP two-factor authentication |
| `qrcode` / `Pillow` | QR code generation for 2FA enrollment |

---

## 📂 Project Structure

```
SecureFileVault/
│
├── app.py              # Flask web app — routes for register/login/upload/encrypt/decrypt
├── main.py              # CLI entry point — registration, auth, encrypt/decrypt menu
├── auth.py               # User registration, bcrypt + TOTP verification
├── crypto_utils.py        # AES-256 key generation, file encryption/decryption
├── vault.py                # CLI encrypt-to-vault / decrypt-from-vault logic
├── logger.py                # Timestamped activity logging
├── config.py                  # Central path configuration
├── requirements.txt
├── setup_and_run.bat           # Windows: create venv, install deps, run CLI
├── run_web_app.bat              # Windows: activate venv, run Flask app
├── templates/                    # HTML templates (Flask)
│   ├── layout.html               # Base template — nav, flash messages, CSS/font-awesome links
│   ├── login.html                # Username + password + OTP login form
│   ├── register.html             # Registration form + QR code enrollment screen
│   └── dashboard.html            # File upload, local encrypt/decrypt, encrypted file list
├── static/                        # CSS/JS/generated QR codes (Flask)
│   ├── style.css                 # Dark glassmorphism theme
│   └── qr_code.png               # Generated per registration (⚠️ gitignored — contains a live secret)
├── data/                            # user.json + key.bin  (⚠️ gitignored — see Security Notes)
├── vault_files/                      # Per-user encrypted file storage (⚠️ gitignored)
└── logs/                              # activity.log  (⚠️ gitignored)
```

---

## 🚀 How to Run

### Option A — Web App (Flask)

**Windows (automatic):**
```bash
setup_and_run.bat      # first run — creates venv, installs dependencies, runs CLI
run_web_app.bat         # launches the web server
```

**Manual (any OS):**
```bash
git clone https://github.com/HasnainSherazi/SecureFileVault.git
cd SecureFileVault
pip install -r requirements.txt
python app.py
```
Then open `http://127.0.0.1:5000` in your browser. Register, scan the QR code with an authenticator app, then log in with your password + the 6-digit code.

### Option B — CLI

```bash
python main.py
```
Follow the prompts to register (first run) or log in, then choose to encrypt or decrypt a file by path.

---

## 🧠 How It Works

```
Register: Username + Password
              │
              ▼
   bcrypt hash password
   Generate TOTP secret → QR code
              │
              ▼
        Save to data/user.json

Login: Username + Password + 6-digit OTP
              │
              ▼
   Verify bcrypt hash  →  Verify TOTP code
              │
              ▼
          Access granted

Upload a file
              │
              ▼
   AES-256-CFB encrypt (random IV per file)
              │
              ▼
   Stored in vault_files/<username>/filename.enc
```

---

## 🔒 Security Notes & Known Limitations

This project was built for an Information Security course to demonstrate encryption and 2FA concepts. A few design points worth knowing before extending it:

- **Shared encryption key** — all users' files are currently encrypted with a single key stored in `data/key.bin`, rather than a per-user key derived from their password. A stronger design would derive each user's key from their password (e.g. via PBKDF2/scrypt) so no single file grants access to every vault.
- **No integrity check on ciphertext** — AES-CFB provides confidentiality but not authenticity. Switching to an AEAD mode like **AES-GCM** would let the app detect tampered files instead of silently decrypting corrupted data.
- **`local_encrypt` / `local_decrypt` trust any local path** — these routes operate on whatever file path is submitted, with no restriction to a safe directory. Fine for trusted local single-user use; would need path validation before any multi-user or networked deployment.
- **`key.bin` and `user.json` must never be committed** — they're excluded via `.gitignore`, but worth restating: `key.bin` alone can decrypt every file in every vault, so treat it like a password.
- **Flask debug mode** — `app.run(debug=True)` is convenient for development but should be turned off before any real deployment (the Werkzeug debugger can allow remote code execution if left on and exposed).

---

## 🔮 Future Improvements

- [ ] Per-user encryption keys derived from password (PBKDF2/scrypt)
- [ ] Switch to AES-GCM for authenticated encryption
- [ ] Path validation/sandboxing for local encrypt/decrypt routes
- [ ] Password reset flow
- [ ] File sharing between users
- [ ] Dockerize for easy deployment

---

## 👤 Author

**Syed Muhammad Hasnain Sherazi**
AI Undergraduate @ CUST, Islamabad

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat&logo=linkedin&logoColor=white)](https://linkedin.com/in/syed-muhammad-hasnain-sherazi)
[![Hugging Face](https://img.shields.io/badge/Hugging%20Face-FFD21E?style=flat&logo=huggingface&logoColor=black)](https://huggingface.co/HasnainSherazi)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/HasnainSherazi)

---

## 📄 License

This project is open source and available under the [MIT License](LICENSE).
=======
# SecureVault
>>>>>>> 0630ccf1350e688acaadfb3e94fe3e9bb7f5e059
