# PortaCrypt — Secure Browser-Based File Encryption

**A zero-installation, drag-and-drop file encryption tool that runs entirely in your browser.**

PortaCrypt is a serverless, client-side encryption utility designed for maximum portability and security. Carry it on a thumb drive or self-host it. All cryptographic operations happen locally — your files and passwords are never transmitted over the internet.

---

## Key Features

- **Truly Portable:** Runs from a single `.html` file. No installation, no dependencies, no runtime required.
- **Cross-Platform:** Works on any OS with a modern browser (Chrome, Firefox, Edge, Safari).
- **AES-256-GCM Encryption:** Industry-standard authenticated encryption via the browser's native Web Crypto API. Confidentiality and integrity in a single pass — tampered ciphertext is always rejected on decryption.
- **PBKDF2 Key Derivation:** Passwords are never used as raw keys. PBKDF2-SHA256 with a unique random salt per file transforms your password into a strong cryptographic key, significantly raising the cost of brute-force attacks.
- **Password Confirmation:** Encrypt mode requires password confirmation to prevent accidental key mismatch before files are locked.
- **Multi-File & Folder Support:** Encrypt or decrypt multiple files or entire folders at once. Output is bundled into a single `.zip` archive.
- **Zero-Knowledge:** No server receives your data. There is nothing to breach, subpoena, or leak.

---

## How to Use

### Encrypt
1. Select **Encrypt** mode (default).
2. Drag and drop files or a folder onto the upload area, or use **Select Files** / **Select Folder**.
3. Enter a strong password (minimum 8 characters) and confirm it.
4. Click **Encrypt & Download**.
5. Single file → downloads as `filename.ext.enc`. Multiple files → downloads as `encrypted-files.zip` containing individual `.enc` files.

### Decrypt
1. Select **Decrypt** mode.
2. Select your `.enc` file(s).
3. Enter the exact password used during encryption.
4. Click **Decrypt & Download**.
5. Your original file(s) are restored and downloaded.

> If the password is wrong, decryption will fail. AES-GCM authentication guarantees no partial or silent decryption — you either get the correct plaintext or an explicit error.

---

## Technical Specifications

### Cryptography

| Component | Detail |
|---|---|
| Encryption algorithm | AES-256-GCM (authenticated encryption) |
| Key derivation | PBKDF2-SHA256 |
| PBKDF2 iterations | 100,000 |
| Salt | 16 bytes, randomly generated per file |
| IV | 12 bytes, randomly generated per file |
| Auth tag | 16 bytes (GCM), appended to ciphertext |
| Crypto engine | Browser Web Crypto API (`crypto.subtle`) |
| Key extractability | `false` — derived key cannot be exported from the browser |

### `.enc` File Format

Output files are binary with the following layout, compatible with the original Python version:

```
[ 16-byte SALT | 12-byte IV | 16-byte AUTH_TAG | ENCRYPTED_DATA ]
```

### Stack

| Layer | Technology |
|---|---|
| UI | HTML5, Tailwind CSS, Material Symbols, Manrope / Inter fonts |
| Crypto | Web Crypto API (`crypto.subtle`) |
| Archiving | JSZip 3.10.1 (SRI-verified CDN load) |

---

## Security Model

1. **Client-side only.** No files, passwords, or metadata leave the browser. The app runs entirely in the browser's sandboxed JS environment.
2. **No data retention.** Nothing is stored after you close the tab.
3. **Non-extractable keys.** The derived AES key is created with `extractable: false` — it cannot be exported from the Web Crypto API by any script, including a compromised dependency.
4. **SRI on dependencies.** JSZip is loaded from CDN with a `sha384` Subresource Integrity hash. The browser rejects the script if the CDN serves tampered content.
5. **Safe error messages.** Errors shown to the user never expose internal exception details — raw error messages are console-only.
6. **Password is key.** There is no recovery mechanism. If the password is lost, the encrypted file cannot be recovered. This is intentional.

> **Threat model note:** Tailwind CSS is loaded from `cdn.tailwindcss.com`, which does not support SRI (dynamic bundle). It controls only styling, not crypto logic. For maximum security, self-host the Tailwind build.

---

## Self-Hosting with Docker

PortaCrypt can be self-hosted as a lightweight Docker container served by nginx.

```
portacrypt/
├── Dockerfile
├── docker-compose.yml
└── PortaCrypt.html
```

**Dockerfile**
```dockerfile
FROM nginx:alpine
COPY PortaCrypt.html /usr/share/nginx/html/index.html
EXPOSE 80
```

**docker-compose.yml**
```yaml
services:
  portacrypt:
    build: .
    container_name: portacrypt
    restart: unless-stopped
    ports:
      - "8080:80"
    healthcheck:
      test: ["CMD", "wget", "-q", "--spider", "http://localhost:80"]
      interval: 30s
      timeout: 5s
      retries: 3
```

**Deploy:**
```bash
docker compose up -d --build
```

The app will be available at `http://localhost:8080`. Place a reverse proxy (Caddy, Nginx, Traefik) in front for HTTPS.

---

## Project Evolution

| Version | Description |
|---|---|
| v1 — Python/Flask | Server-side encryption using Flask + PyCryptodome. Required Python and library installation on the host. |
| v2 — Standalone HTML | Re-engineered as a single client-side file. Eliminated the server entirely. Runs from a thumb drive on any machine with a browser. |
| v3 — Docker | Containerized with nginx:alpine for easy self-hosting behind a reverse proxy. |

**Why the migration from Python to browser-native:**
- True portability — no interpreter or libraries required on the host machine.
- Improved security posture — no server attack surface.
- Simpler distribution — a single `.html` file is easier to verify and share than a multi-file server project.

---

## Disclaimer

Provided "as is" without warranty. Always retain a backup of original unencrypted files. The user is solely responsible for password management and the security of encrypted data.
