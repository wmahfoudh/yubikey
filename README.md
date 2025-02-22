# The Ultimate YubiKey 5 Masterclass – From Zero to Hero 🔐

*Everything you need to know to become a YubiKey power user*

---

## Table of Contents

1. [Introduction](README.md#introduction)
2. [Understanding YubiKey](README.md#understanding-yubikey)
   - [What Is a YubiKey?](README.md#what-is-a-yubikey)
   - [Cryptographic Foundations](README.md#cryptographic-foundations)
3. [Core Features & Real-World Usage](README.md#core-features--real-world-usage)
   - [FIDO2 / WebAuthn – Passwordless & Two-Factor Authentication](README.md#fido2--webauthn)
   - [Smart Card (PIV) for Secure Logins and SSH](README.md#smart-card-piv)
   - [OpenPGP – Encryption, Signing & SSH](README.md#openpgp)
   - [TOTP – Authenticator Replacement](README.md#totp)
   - [Challenge-Response – For KeepassXC and LUKS](README.md#challenge-response)
   - [Static Password on Touch – HID Emulation](README.md#static-password-on-touch)
4. [Backup, Duplication & Recovery](README.md#backup-duplication--recovery)
   - [General Backup Strategies](README.md#general-backup-strategies)
   - [Duplicating Credentials for PIV, OpenPGP, TOTP & Challenge-Response](README.md#duplicating-credentials)
   - [Resetting and Restoring YubiKeys](README.md#resetting-and-restoring-yubikeys)
5. [Advanced Configurations & Security Considerations](README.md#advanced-configurations--security-considerations)
   - [Configuring Multiple Security Modes](README.md#configuring-multiple-security-modes)
   - [Full Disk Encryption with LUKS](README.md#full-disk-encryption-with-luks)
   - [Protecting SSH Keys with Touch Authentication](README.md#protecting-ssh-keys-with-touch-authentication)
   - [Advanced PIV Usage](README.md#advanced-piv-usage)
6. [Practical Implementations with Real Services](README.md#practical-implementations-with-real-services)
   - [FIDO2 / WebAuthn for Web Logins](README.md#fido2--webauthn-for-web-logins)
   - [Using YubiKey for Windows and macOS Login](README.md#using-yubikey-for-windows-and-macos-login)
   - [Integrating OpenPGP for Git Signing and Email Encryption](README.md#integrating-openpgp-for-git-signing-and-email-encryption)
   - [TOTP for Two-Factor Authentication](README.md#totp-for-two-factor-authentication)
   - [Challenge-Response with KeepassXC and LUKS](README.md#challenge-response-with-keepassxc-and-luks)
7. [Troubleshooting & Maintenance](README.md#troubleshooting--maintenance)
8. [Advanced Custom Setups, Scripting & Enterprise Deployments](README.md#advanced-custom-setups-scripting--enterprise-deployments)
   - [Automating YubiKey Logins and Workflows](README.md#automating-yubikey-logins-and-workflows)
   - [Enterprise Deployment and Multi-User Provisioning](README.md#enterprise-deployment-and-multi-user-provisioning)
9. [Conclusion](README.md#conclusion)

---

## Introduction

In today’s digital world, passwords alone are no longer enough. Phishing, keylogging, and weak passwords can jeopardize your security. **YubiKeys** provide robust, hardware-based authentication to protect your accounts and data. This masterclass covers everything from the cryptographic foundations to advanced enterprise integrations, ensuring you become a YubiKey power user. 🚀

---

## Understanding YubiKey

### What Is a YubiKey?

A **YubiKey** is a hardware security module that supports multiple authentication methods:

- **FIDO2 / WebAuthn** (Passwordless login & 2FA)
- **Smart Card (PIV) / PKI Authentication**
- **OpenPGP (GPG Encryption & Signing)**
- **TOTP/HOTP** (Replacing mobile authenticators)
- **Challenge-Response** (For applications like LUKS and KeepassXC)
- **Static Password on Touch** (HID keyboard emulation)

### Cryptographic Foundations

YubiKeys leverage strong cryptographic algorithms to secure your credentials:

| **Feature**           | **Algorithms Used**                                    |
|-----------------------|--------------------------------------------------------|
| FIDO2 / U2F           | ECC (P-256, P-384), RSA-2048                           |
| Smart Card (PIV)      | RSA-2048, ECC (P-256, P-384)                           |
| OpenPGP               | RSA-4096, ECC                                          |
| TOTP/HOTP             | HMAC-SHA1 (RFC 4226, RFC 6238)                           |
| Challenge-Response    | HMAC-SHA1 (RFC 2104)                                   |
| Yubico OTP            | AES-128 Challenge-Response                             |

These algorithms help safeguard against phishing, keyloggers, and unauthorized access. 🔒

---

## Core Features & Real-World Usage

### FIDO2 / WebAuthn

**Use Case:** Secure logins for online services without relying on traditional passwords.

- **Supported by:** Google, Microsoft, GitHub, Bitwarden, Facebook, Twitter, Dropbox
- **Mobile Compatibility:**  
  - **Android:** USB-C, NFC  
  - **iPhone:** NFC or Lightning (YubiKey 5Ci)

**Setup on Arch Linux:**

```sh
sudo pacman -S libfido2
fido2-token -L
```

---

### Smart Card (PIV)

**Use Case:** Use the YubiKey as a physical smart card for authentication on Windows, macOS, and SSH.

- **Supported by:** Windows Login, macOS Login, SSH Authentication

**Setup for SSH Authentication on Arch Linux:**

```sh
ykman piv keys generate 9a pubkey.pem
```

_Add the public key to `~/.ssh/authorized_keys` and authenticate using the YubiKey._

---

### OpenPGP

**Use Case:** Encrypt emails, sign Git commits, and authenticate SSH.

- **Supported by:** ProtonMail, Thunderbird, GitHub, GitLab

**Setup for Git Signing:**

```sh
gpg --card-edit
admin
keytocard
```

Then configure Git:

```sh
git config --global user.signingkey <GPG_KEY_ID>
git commit -S -m "Signed commit"
```

---

### TOTP

**Use Case:** Replace mobile authenticators like Google Authenticator with YubiKey-managed codes.

- **Supported by:** Google, Facebook, Reddit, AWS, PayPal

**Setup on Arch Linux:**

```sh
ykman oath accounts add google --secret <BASE32_SECRET>
ykman oath code google
```

---

### Challenge-Response

**Use Case:** Enhance security for applications like KeepassXC and LUKS full disk encryption.

**Setup for KeepassXC:**

```sh
ykman otp chalresp --touch --generate 2
```

_Then, within KeepassXC, add the challenge-response entry from YubiKey Slot 2._

---

### Static Password on Touch

**Use Case:** Use YubiKey as a secure HID device that types a stored password when touched. Ideal for legacy systems that do not support modern MFA methods.

**Configuration on Arch Linux:**

- **Set a custom static password for Slot 1:**

  ```sh
  ykman otp static --keyboard-layout us 1
  ```
  
- **Generate a random static password for Slot 2:**

  ```sh
  ykman otp static --generate 2 --length 32
  ```

- **Review slot configuration:**

  ```sh
  ykman otp info
  ```

*Security Note:* Consider combining a manually typed prefix with the static password stored on the YubiKey for added security. 🔑

---

## Backup, Duplication & Recovery

### General Backup Strategies

- **Always register at least two YubiKeys per account.**
- **For FIDO2 accounts:** Register multiple keys directly.
- **For Smart Card (PIV) and OpenPGP:** Duplicate keys and certificates across devices.
- **For TOTP and Challenge-Response:** Store the same secret on multiple YubiKeys.

### Duplicating Credentials

- **PIV (Smart Card):**

  ```sh
  ykman piv keys generate --algorithm RSA2048 9a primary-pubkey.pem
  ykman piv certificates export 9a primary-cert.pem
  # On the backup key:
  ykman piv keys import 9a primary-key.pem
  ykman piv certificates import 9a primary-cert.pem
  ```

- **OpenPGP:**

  ```sh
  gpg --export-secret-keys --armor > master-key.asc
  # Import and move keys to the backup YubiKey:
  gpg --import master-key.asc
  gpg --edit-key <YOUR_KEY_ID>
  keytocard
  ```

- **TOTP:**

  ```sh
  ykman oath accounts add my-service --secret <TOTP_SECRET>
  ```

- **Challenge-Response:**

  ```sh
  ykman otp chalresp --touch --generate 2
  # Copy the same secret to the backup key:
  ykman otp chalresp --touch 2
  ```

### Resetting and Restoring YubiKeys

- **Factory Reset (Erases All Data):**

  ```sh
  ykman reset
  ```

- **Reset Individual Components:**

  - OpenPGP:

    ```sh
    ykman openpgp reset
    ```

  - PIV:

    ```sh
    ykman piv reset
    ```

  - FIDO2:

    ```sh
    ykman fido reset
    ```

  - OTP Slots:

    ```sh
    ykman otp delete 1
    ykman otp delete 2
    ```

---

## Advanced Configurations & Security Considerations

### Configuring Multiple Security Modes

Your YubiKey can support several functions simultaneously. To tailor its functionality:

- **List current configurations:**

  ```sh
  ykman config list
  ```

- **Enable FIDO2 and PIV while disabling OTP:**

  ```sh
  ykman config usb --enable FIDO2,PIV --disable OTP
  ```

*Benefit:* Reducing enabled features minimizes the attack surface. 👍

### Full Disk Encryption with LUKS

Bind your LUKS-encrypted disk to your YubiKey using challenge-response:

1. **Install dependencies on Arch Linux:**

   ```sh
   sudo pacman -S yubikey-luks
   ```

2. **Add a YubiKey-secured slot to LUKS:**

   ```sh
   sudo cryptsetup luksAddKey /dev/sdX --key-file <(ykchalresp -2)
   ```

3. **At boot, insert the YubiKey to unlock the disk.**

*Note:* Losing the YubiKey can lock you out; maintain a backup secret.

### Protecting SSH Keys with Touch Authentication

Ensure your YubiKey requires physical touch before using stored SSH keys:

```sh
ykman piv set-touch-policy 9a always
```

### Advanced PIV Usage

- **Understanding PIV PINs:**
  - **PIN:** A 6-digit code to authenticate.
  - **PUK:** An 8-digit reset code.
  - **Management Key:** A 24-byte key for administrative tasks.

- **Reset PIV PIN if needed:**

  ```sh
  ykman piv access unblock-pin
  ```

- **Factory Reset the PIV Module:**

  ```sh
  ykman piv reset
  ```

---

## Practical Implementations with Real Services

### FIDO2 / WebAuthn for Web Logins

1. **On Supported Websites (e.g., Google, GitHub):**
   - Navigate to security settings.
   - Register your YubiKey as a security key.
   - Save recovery codes.

2. **Mobile Usage:**
   - **Android:** Tap via NFC.
   - **iPhone:** Use NFC or the Lightning connector with YubiKey 5Ci.

### Using YubiKey for Windows and macOS Login

- **For Windows:**
  - Install Yubico PIV Manager.
  - Generate a PIV key:

    ```sh
    ykman piv keys generate 9a pubkey.pem
    ykman piv certificates generate 9a pubkey.pem
    ```

  - Import the certificate into Windows via MMC.

- **For macOS:**
  - Follow similar steps using certificate-based authentication.

### Integrating OpenPGP for Git Signing and Email Encryption

1. **Move your GPG key to the YubiKey:**

   ```sh
   gpg --card-edit
   admin
   keytocard
   ```

2. **Configure Git to sign commits:**

   ```sh
   git config --global user.signingkey <GPG_KEY_ID>
   git commit -S -m "Signed commit"
   ```

### TOTP for Two-Factor Authentication

1. **Add your TOTP account:**

   ```sh
   ykman oath accounts add google --secret <BASE32_SECRET>
   ```

2. **Generate codes as needed:**

   ```sh
   ykman oath code google
   ```

*Tip:* Use Yubico Authenticator on mobile devices.

### Challenge-Response with KeepassXC and LUKS

- **For KeepassXC:**

  ```sh
  ykman otp chalresp --touch --generate 2
  ```

  _Then configure within KeepassXC._

- **For LUKS Disk Encryption:**
  - Follow the full disk encryption steps detailed earlier.

---

## Troubleshooting & Maintenance

### Common Issues

- **YubiKey Not Detected:**
  - Check USB connections or try a different port.
  - Restart the PC/SC daemon:

    ```sh
    sudo systemctl restart pcscd
    ```

  - Review system logs:

    ```sh
    dmesg | grep -i yubikey
    ```

- **FIDO2/WebAuthn Issues in Browsers:**
  - Ensure WebAuthn is enabled in your browser’s settings.
  - Reload udev rules on Arch Linux:

    ```sh
    sudo udevadm control --reload-rules && sudo udevadm trigger
    ```

- **SSH Authentication Failures:**
  - Restart the GPG agent:

    ```sh
    gpgconf --kill gpg-agent
    export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
    ```
  - Add your YubiKey public key:

    ```sh
    ssh-keygen -D /usr/lib/libykcs11.so >> ~/.ssh/authorized_keys
    ```

- **Resetting Components:**
  - Use the appropriate `ykman` reset commands as needed (see Backup & Recovery section).

---

## Advanced Custom Setups, Scripting & Enterprise Deployments

### Automating YubiKey Logins and Workflows

- **SSH Auto-Login with YubiKey (FIDO2 SSH Key):**

  ```sh
  ssh-keygen -t ed25519-sk -O resident -O verify-required -f ~/.ssh/id_yubikey
  ssh-copy-id -i ~/.ssh/id_yubikey.pub user@server.com
  export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
  ssh user@server.com
  ```

- **Auto-type Static Password (Linux Automation):**

  Create a udev rule to copy an OTP to your clipboard:

  ```sh
  echo 'ACTION=="add", ATTRS{idVendor}=="1050", ATTRS{idProduct}=="0407", RUN+="/usr/bin/ykman otp code 1 | xclip -selection clipboard"' | sudo tee /etc/udev/rules.d/99-yubikey.rules
  sudo udevadm control --reload-rules && sudo udevadm trigger
  ```

- **Windows Auto-Login via PowerShell:**

  ```powershell
  Get-Content "C:\yubikey_otp.txt" | Set-Clipboard
  ```

### Enterprise Deployment and Multi-User Provisioning

- **YubiKey with Active Directory:**
  - Install YubiKey smart card middleware (use the official Yubico tools).
  - In Group Policy, enable “Interactive logon: Require smart card.”
  - Issue smart card certificates via AD Certificate Services.

- **Linux LDAP Authentication with YubiKey:**
  - Install the PAM module:

    ```sh
    sudo pacman -S libpam-yubico
    ```

  - Configure PAM (e.g., edit `/etc/pam.d/sshd`) to include:

    ```sh
    auth required pam_yubico.so id=16 debug authfile=/etc/yubikey_mappings
    ```

- **VPN Authentication with YubiKey:**
  - Install OpenVPN and OpenSC:

    ```sh
    sudo pacman -S openvpn opensc
    ```

  - Identify your YubiKey’s PKCS#11 ID:

    ```sh
    openvpn --show-pkcs11-ids /usr/lib/opensc-pkcs11.so
    ```

  - Modify your OpenVPN config to include the PKCS#11 settings.

### Custom Shell Scripting for YubiKey Workflows

- **Auto-Generate OTP on Demand:**

  Create a script (`otp.sh`):

  ```sh
  #!/bin/bash
  echo "Generating OTP..."
  ykman otp code 2 | xclip -selection clipboard
  notify-send "OTP Copied!"
  ```

- **Automate Git Commit Signing:**

  Configure Git globally to sign commits:

  ```sh
  git config --global user.signingkey YOUR_KEY_ID
  git config --global commit.gpgsign true
  ```

### Enterprise-Grade Security with YubiHSM

- **Using YubiHSM for Key Storage:**

  ```sh
  sudo pacman -S yubihsm-shell
  yubihsm-shell -a set-authkey -i 1 -p "supersecurepassword"
  ssh-keygen -t rsa -b 4096
  yubihsm-shell -a import-object -i 0x1234 -k ssh_key.pem
  ```

- **Automating API Authentication with YubiKey:**

  Generate a FIDO2-backed API key:

  ```sh
  ykman fido credentials generate --pin
  ```

  Then use it in API requests:

  ```sh
  curl -H "Authorization: Bearer $(ykman fido sign)" https://api.example.com/secure-data
  ```

---

## Conclusion

This masterclass has taken you from the basics of YubiKey technology to advanced configurations, troubleshooting, and enterprise-level deployments. You now understand:

- The **cryptographic foundations** behind YubiKey’s diverse authentication methods.
- How to implement **FIDO2, Smart Card (PIV), OpenPGP, TOTP, Challenge-Response, and Static Password** functionalities.
- Strategies for **backup, duplication, and recovery**.
- Advanced techniques for **securing SSH, full disk encryption, and enterprise integration**.
- Custom automation and scripting to streamline your security workflows.

By leveraging these techniques on Arch Linux (and other systems), you can significantly enhance your digital security posture. Now, go forth and secure your digital life with YubiKey! 🚀🔐

Enjoy your journey to becoming a YubiKey power user!
