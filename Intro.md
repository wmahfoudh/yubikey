
# 📌 Introduction

You have purchased **YubiKey 5C** devices, and instead of just following tutorials, you want a deep understanding of their **technologies, cryptographic foundations, and practical use cases** before deploying them. This training will provide an **in-depth, structured learning path** focusing on both theory and implementation.

Assumed **primary system** is **Arch Linux**, but you also have **Windows**. We'll cover both environments for full flexibility.

---

## **🔹 Phase 1: Understanding the Foundations**

### **1️⃣ What is a YubiKey?**

A **YubiKey** is a **hardware security module** that provides multiple authentication mechanisms, including:

- **One-Time Passwords (OTP)**
- **Universal 2nd Factor (U2F) / FIDO2**
- **Public Key Infrastructure (PKI) / Smart Card**
- **OpenPGP for Encryption & Signing**
- **HOTP/TOTP (Authenticator replacement)**

Each of these technologies is backed by specific cryptographic protocols.

---

### **2️⃣ Cryptographic Foundations Behind YubiKey**

Before diving into the YubiKey’s features, let’s briefly cover the underlying **cryptographic algorithms** used:

|**Feature**|**Core Cryptography Used**|**Use Case**|
|---|---|---|
|**FIDO2 / U2F**|RSA-2048, ECC (P-256, P-384)|Passwordless login, 2FA|
|**Smart Card (PIV)**|RSA-2048, ECC|Secure SSH, Windows login|
|**OpenPGP**|RSA-4096, ECC|Email encryption, signing|
|**TOTP/HOTP**|HMAC-SHA1 (RFC 4226, RFC 6238)|Google Authenticator replacement|
|**Yubico OTP**|AES-128 Challenge-Response|Legacy OTP authentication|

Each of these protocols offers a different way to **securely authenticate users and devices**.

---

## **🔹 Phase 2: Features & In-Depth Technical Analysis**

### **1️⃣ FIDO2 & U2F – Passwordless Authentication**

🔹 **What it is**:

- **FIDO2** is a modern authentication standard allowing **passwordless login**.
- **U2F** is a 2FA system that strengthens traditional passwords.

🔹 **How it works**:

- Uses **public-key cryptography**.
- When registering a website, the YubiKey generates a **key pair**:
    - **Public key** → stored on the server.
    - **Private key** → stored securely in YubiKey.
- On authentication, the server sends a **challenge**, and the YubiKey **signs it** with the private key.

🔹 **Example use case**:

- **GitHub, Google, Microsoft** accounts support FIDO2 login.

🔹 **Setup on Arch Linux**:

```sh
sudo pacman -S libfido2
fido2-token -L
```

🔹 **Algorithm Behind It**:

- Uses **ECC (Elliptic Curve Cryptography)** or **RSA**.
- Challenge-Response mechanism ensures resistance against phishing.

---

### **2️⃣ Smart Card (PIV) – Secure Authentication & Cryptographic Storage**

🔹 **What it is**:

- **Personal Identity Verification (PIV)** is a standard for **smart card authentication**.
- Can be used for **SSH, GPG key storage, Windows login**.

🔹 **How it works**:

- YubiKey acts as a **hardware token** storing **private keys**.
- Instead of storing keys on disk, they are stored **inside the secure chip**.

🔹 **Example use cases**:

- **SSH key storage** to protect against private key theft.
- **Login to Windows using Smart Card authentication**.

🔹 **Setup on Arch Linux**:

```sh
sudo pacman -S yubikey-manager
ykman piv info
```

🔹 **Algorithm Behind It**:

- Uses **RSA-2048, ECC (P-256, P-384)**.
- Works as **PKCS#11 hardware module**.

---

### **3️⃣ OpenPGP – Email Encryption & Signing**

🔹 **What it is**:

- **OpenPGP** allows you to encrypt emails, sign commits, and authenticate SSH.

🔹 **How it works**:

- YubiKey generates a **GPG keypair**.
- The **private key never leaves the YubiKey**.
- When signing/encrypting, the YubiKey uses its secure environment.

🔹 **Example use cases**:

- **GPG signing** for emails, software packages, or Git commits.
- **SSH authentication** instead of standard SSH keys.

🔹 **Setup on Arch Linux**:

```sh
sudo pacman -S gnupg yubikey-manager
gpg --card-status
```

🔹 **Algorithm Behind It**:

- Uses **RSA-4096** or **ECC**.
- Ensures **non-repudiation** and **secure identity verification**.

---

### **4️⃣ OTP (TOTP/HOTP/Yubico OTP) – Secure Login Codes**

🔹 **What it is**:

- YubiKey can **replace mobile authenticators** like Google Authenticator.
- Supports **TOTP (time-based)** and **HOTP (counter-based)**.
- Yubico OTP is a **legacy AES-based** authentication protocol.

🔹 **How it works**:

- For **TOTP**, YubiKey generates **time-sensitive** 6-digit codes.
- For **HOTP**, YubiKey generates **counter-based** 6-digit codes.

🔹 **Example use cases**:

- Replace **Google Authenticator** for websites.
- Use **Yubico OTP** for **legacy authentication**.

🔹 **Setup on Arch Linux**:

```sh
sudo pacman -S yubikey-manager yubikey-personalization
ykman otp info
```

🔹 **Algorithm Behind It**:

- **TOTP** uses **HMAC-SHA1**.
- **HOTP** is based on **RFC 4226**.

---

## **🔹 Phase 3: Practical Implementations**

Now that we've covered the **theory**, let's focus on **real-world deployments**.

### **1️⃣ Secure SSH Authentication**

🔹 **Why?**

- Traditional SSH keys are **stored on disk** → If compromised, an attacker gains access.
- With **YubiKey PIV**, the key is **inside the hardware**, making it impossible to steal.

🔹 **How to Implement on Arch Linux**:

1. Install dependencies:

```sh
sudo pacman -S openssh yubikey-manager
```

1. Configure SSH to use the YubiKey:

```sh
ykman piv generate-key --algorithm RSA2048 --touch-policy always 9a pubkey.pem
```

1. Add it to the SSH agent:

```sh
ssh-add -s /usr/lib/libykcs11.so
```

---

### **2️⃣ Passwordless Login to Websites**

🔹 **Why?**

- **Phishing-resistant**.
- **No passwords to remember**.
- **Impossible to duplicate keys**.

🔹 **How to Implement**:

1. Go to a **website that supports FIDO2** (Google, GitHub, Microsoft).
2. Register your YubiKey as a **security key**.

---

### **3️⃣ Using YubiKey as a GPG Smart Card**

🔹 **Why?**

- Encrypt files and emails **without exposing private keys**.
- **Git commit signing**.

🔹 **How to Implement**:

1. Generate a GPG key:

```sh
gpg --full-generate-key
```

1. Move it to the YubiKey:

```sh
gpg --edit-key YOUR_KEY_ID
```

---

## **📌 Conclusion**

We've seen:

- The **cryptography** behind each authentication mechanism.
- How to **securely store keys** and use them for SSH, GPG, and website authentication.
- **Practical deployments** on **Arch Linux & Windows**.
