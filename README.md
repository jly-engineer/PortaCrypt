# PortaCrypt - A Portable File Encryption Tool

**A secure, serverless, browser-based utility for encrypting and decrypting files on any computer.**

PortaCrypt is a zero-installation, drag-and-drop file encryption tool designed for maximum portability and security. It runs entirely within a web browser, meaning you can carry it on a thumb drive and use it on any modern Windows, macOS, or Linux computer without needing to install any software. All cryptographic operations happen locally on your machine, ensuring your files and passwords are never sent over the internet.

## Key Features

*   🚀 **Truly Portable:** Runs from a single `index.html` file on a thumb drive. No installation or special software is required.
*   💻 **Cross-Platform:** Works on any operating system with a modern web browser (Chrome, Firefox, Edge, Safari).
*   🔒 **Strong Encryption:** Utilizes the industry-standard AES-256-GCM algorithm via the browser's native Web Crypto API for authenticated encryption.
*   🔑 **Secure Key Derivation:** Passwords are never used directly. A strong cryptographic key is derived using PBKDF2 with 100,000 iterations to protect against brute-force attacks.
*   📁 **Multi-File & Folder Support:** Encrypt or decrypt multiple files or entire folders at once. The output is conveniently bundled into a single `.zip` archive.
*   🌐 **Serverless & Offline-Capable:** No server is involved. All processing is done on the client-side. After the first use (which caches the UI libraries), it can function completely offline.
*   ✨ **Simple Interface:** A clean, modern, drag-and-drop interface makes the tool intuitive and easy to use for everyone.

## How to Use

PortaCrypt is designed to be as simple as possible.

1.  **Save the File:** Store the `index.html` file on a portable storage device like a thumb drive.
2.  **Open on Any Computer:** Plug the thumb drive into any computer and double-click the `index.html` file. It will open in the default web browser.
3.  **To Encrypt Files:**
    *   Ensure the **Encrypt** mode is selected.
    *   Drag and drop your files/folder onto the upload area, or use the "Select File(s)" / "Select Folder" buttons.
    *   Enter a strong password (minimum 8 characters).
    *   Click the **Encrypt & Download** button.
    *   A `.enc` file (or a `.zip` archive containing multiple `.enc` files) will be downloaded to your computer.
4.  **To Decrypt Files:**
    *   Select the **Decrypt** mode.
    *   Select the `.enc` file(s) you wish to decrypt.
    *   Enter the **exact same password** used for encryption.
    *   Click the **Decrypt & Download** button.
    *   Your original, decrypted file(s) will be downloaded.

## Technical Specifications

This tool adheres to modern, robust cryptographic standards to ensure the integrity and confidentiality of your data.

#### Cryptography

*   **Encryption Algorithm:** **AES-256-GCM** (Galois/Counter Mode). This is an authenticated encryption (AEAD) cipher, which means it protects against both eavesdropping (confidentiality) and tampering (integrity).
*   **Key Derivation Function:** **PBKDF2-SHA256**.
    *   **Iterations:** 100,000
    *   **Salt:** A random 16-byte salt is generated for every encryption, ensuring that encrypting the same file with the same password results in a different output each time.
*   **Initialization Vector (IV):** A random 12-byte IV is generated for every encryption, as recommended for AES-GCM.
*   **Engine:** All cryptographic operations are performed by the browser's built-in and highly vetted **Web Crypto API**.

#### File Structure (`.enc`)

The output `.enc` files are structured in a specific way to be compatible with the original Python version and to contain all necessary data for decryption:

`[ 16-byte SALT | 12-byte IV | 16-byte AUTH_TAG | ENCRYPTED_DATA ]`

#### Core Technologies

*   **Frontend:** Plain HTML5, JavaScript (ES6+), and Tailwind CSS for styling.
*   **File Archiving:** The [JSZip](https://stuk.github.io/jszip/) library is used to create `.zip` archives directly in the browser when handling multiple files.

## Security Model

Your security is the primary design consideration.

1.  **Client-Side Only:** No files, passwords, or metadata ever leave your computer. The application logic runs entirely within your browser's secure sandbox.
2.  **No Data Retention:** The application does not store or remember any files or passwords after you close the browser tab.
3.  **Password is Key:** The security of your encrypted file is entirely dependent on the strength of your password. **If you forget your password, there is absolutely no way to recover the file.** This is a feature, not a bug.

## Project Evolution

This project was originally a Python-based web application using the Flask framework. It was re-engineered into a standalone client-side tool for the following reasons:

*   **Enhanced Portability:** The Python version required the host machine to have a Python interpreter and specific libraries installed. The new JavaScript version runs on any machine with a modern web browser, fulfilling the goal of a true "run from a thumb drive" utility.
*   **Improved Security Posture:** By eliminating the server backend, we remove an entire class of potential vulnerabilities. There is no server to attack or misconfigure.
*   **Simplicity:** A single file is easier to manage, share, and verify than a multi-file project with server dependencies.

## Disclaimer

This tool is provided "as is" without warranty of any kind. Always keep a backup of your original, unencrypted files. The user is solely responsible for remembering their password and for the security of the data they encrypt.

---
