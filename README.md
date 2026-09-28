# Web-Based Text Encryption Tool

A single-page web application where users type text, choose an encryption
algorithm from a dropdown, and click a button to get the ciphertext. Built for the
*Information Security* course (Assignment 2).

**Stack:** HTML + CSS + JavaScript frontend, Node.js + Express backend, Node `crypto`
and CryptoJS for the algorithms.

## Features

**Core requirements**

- Text box for input, algorithm dropdown, encrypt button, and a result area
- Seven algorithms: Caesar, Vigenere, Base64, AES-256-GCM, DES, RSA-2048 (OAEP), SHA-256
- Validation: empty input is blocked and an algorithm must be selected (checked in
  the browser and again on the server); missing or invalid keys give clear messages

**Bonus features (all four implemented)**

- Decryption for every reversible algorithm
- SHA-256 hashing option (one-way, so Decrypt is disabled for it)
- One-click "Copy result" button, plus "Use result as input" for quick round trips
- User accounts (register / login / logout) to save encrypted messages
- Two-panel interface with a light theme by default and a one-click dark mode (the choice is remembered)

## Algorithms

| Algorithm | Type | Key | Notes |
|---|---|---|---|
| Caesar | Classical | Integer shift | Educational only, 25 possible keys |
| Vigenere | Classical | Letter keyword | Educational only, breakable by Kasiski analysis |
| Base64 | Encoding | None | Not encryption, anyone can decode it |
| AES-256-GCM | Symmetric | Passphrase | Key from scrypt + random salt, random IV, authentication tag |
| DES | Symmetric (legacy) | Passphrase | 56-bit key, insecure, included for comparison |
| RSA-2048 OAEP | Asymmetric | Key pair (generated in the UI) | Max 190 bytes per message |
| SHA-256 | Hash | None | One-way |

## Getting Started

Requires [Node.js](https://nodejs.org) 18 or newer.

```bash
git clone https://github.com/<your-username>/Web-Based-Text-Encryption-Tool.git
cd Web-Based-Text-Encryption-Tool
npm install
npm start
```

Then open **http://localhost:3000** in your browser. Set a different port with
`PORT=4000 npm start` (macOS/Linux) or `set PORT=4000 && npm start` (Windows cmd).

### Run the tests

```bash
npm test
```

15 automated tests cover the ciphers (against published test vectors such as the
NIST SHA-256 vector and the Vigenere LEMON example), input validation, the API,
registration/login, access control and login lockout.

## How to Use

1. Type text into **Input text**.
2. Pick a method from **Encryption method**. Key fields appear for the methods that need one.
3. Press **Encrypt** (or **Hash** for SHA-256). The result appears in **Result**.
4. Press **Copy result** to copy it. To decrypt, press **Use result as input**, enter the
   same key and press **Decrypt**.
5. For RSA, press **Generate RSA key pair**, encrypt with the public key, decrypt with the private key.
6. Optional: **Log in / Register** to save ciphertext to your account and reload it later.

## Project Structure

```
Web-Based-Text-Encryption-Tool/
├── README.md
├── package.json
├── server/
│   ├── server.js        Express app: API routes, validation, security headers
│   ├── ciphers.js       All encryption logic (one registry entry per algorithm)
│   └── auth.js          Accounts (scrypt), sessions, saved-message storage
├── public/              Frontend: index.html, style.css, app.js, theme.js
├── tests/               ciphers.test.js, api.test.js
├── report/              Short report (Word) on the encryption techniques
├── demo/
│   ├── demo.mp4         Demo video (silent, with on-screen captions)
│   ├── DEMO_SCRIPT.md   Narration script if you want to re-record with voice
│   └── record_demo.py   Script that drives the app and records the video
└── docs/screenshots/    Screenshots used in the report
```

## API

| Method | Endpoint | Purpose |
|---|---|---|
| GET | `/api/algorithms` | Algorithm catalog (drives the dropdown and key fields) |
| POST | `/api/encrypt` | `{ algorithm, text, options }` returns `{ result }` |
| POST | `/api/decrypt` | Same shape as encrypt |
| POST | `/api/rsa/generate` | Generates an RSA-2048 key pair |
| POST | `/api/auth/register`, `/api/auth/login`, `/api/auth/logout` | Accounts |
| GET / POST / DELETE | `/api/messages` | Saved ciphertext (login required) |

## Security Notes

- Passwords are hashed with scrypt and a per-user salt; sessions use random 256-bit tokens.
- Only ciphertext is saved. Plain text and keys are never written to disk.
- Strict Content-Security-Policy, `X-Content-Type-Options` and `X-Frame-Options` headers;
  the UI never uses `innerHTML` with user data.
- Keys are sent to the local server to perform the operation. For a real deployment
  this must run behind HTTPS (or move the cryptography into the browser with the Web Crypto API).
- Caesar, Vigenere, Base64 and DES are included to teach, not to protect real secrets.

## Regenerating the demo video (optional)

```bash
pip install playwright && playwright install chromium
python demo/record_demo.py
```

## Author

Maira Imtiaz, BSCS 8th Semester
Course: Information Security, Assignment 2
