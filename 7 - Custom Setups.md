# **🔹 Advanced YubiKey Custom Setups, Scripting & Enterprise-Level Configurations**

_How to fully automate, customize, and deploy YubiKey in high-security environments_

---

## **📌 Why Go Beyond Standard YubiKey Usage?**

For advanced users, **YubiKey can be scripted, automated, and deployed at scale**. This guide will cover:

✅ **Automating YubiKey for seamless authentication & password entry**  
✅ **Using YubiKey in enterprise environments (LDAP, Active Directory, remote access)**  
✅ **Custom shell scripting to integrate YubiKey into workflows**  
✅ **Securing multiple YubiKeys with key duplication & backups**

---

## **🔹 1️⃣ Automating YubiKey for Faster Logins & Authentication**

### **🔹 Automating SSH Login with YubiKey (No Passwords Required)**

🔹 **Goal:** SSH into remote servers **without entering a password**—YubiKey handles authentication.

🔧 **Step 1: Generate a FIDO2 SSH Key on YubiKey**

```bash
ssh-keygen -t ed25519-sk -O resident -O verify-required -f ~/.ssh/id_yubikey
```

📌 **Flags explained:**

- `-t ed25519-sk` → Uses **FIDO2 security key**.
- `-O resident` → Stores the key **directly on the YubiKey**.
- `-O verify-required` → Requires **YubiKey touch for every login**.

🔧 **Step 2: Add the Public Key to Remote Server**

```bash
ssh-copy-id -i ~/.ssh/id_yubikey.pub user@server.com
```

🔧 **Step 3: Enable YubiKey for SSH Authentication**

```bash
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
```

Now, simply run:

```bash
ssh user@server.com
```

🚀 **No password needed—just touch the YubiKey!**

---

### **🔹 Automating Windows Logins with YubiKey (No Password Needed)**

🔹 **Goal:** Log into Windows **by inserting the YubiKey and touching it**.

🔧 **Step 1: Enable YubiKey for Windows Login (Smart Card Mode)**

1. Install **Yubico Login for Windows**:  
    [https://www.yubico.com/support/download/yubico-login-for-windows/](https://www.yubico.com/support/download/yubico-login-for-windows/)
2. Run `ykman piv keys generate` to create a PIV key for authentication:
    
    ```bash
    ykman piv keys generate -a RSA2048 9a public.pem
    ```
    
3. Install the **PIV certificate on Windows** using the YubiKey Manager.

Now, **insert your YubiKey and log in—no password required!**

---

## **🔹 2️⃣ Using YubiKey in Enterprise Environments (LDAP, Active Directory, Remote Access)**

### **🔹 YubiKey with Active Directory (AD) for Enterprise Authentication**

🔹 **Goal:** Use YubiKey as a **second factor** for Windows domain authentication.

🔧 **Step 1: Enable Smart Card Authentication in Active Directory**

1. Open **Group Policy Management (`gpedit.msc`)**.
2. Go to **Computer Configuration → Windows Settings → Security Settings → Local Policies → Security Options**.
3. Set **"Interactive logon: Require smart card" to Enabled**.

🔧 **Step 2: Issue Smart Card Certificates for YubiKey**

1. Open **Active Directory Certificate Services (AD CS)**.
2. Issue a new **PIV smart card certificate** for YubiKey users.

🔧 **Step 3: Require YubiKey for RDP (Remote Desktop Protocol)**

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name SecurityLayer -Value 2
```

Now, **users must insert their YubiKey for authentication**.

---

## **🔹 3️⃣ Custom Shell Scripting for YubiKey Workflows**

### **🔹 Auto-Generate One-Time Password (OTP) on Demand (Linux & MacOS)**

🔹 **Goal:** Generate OTP **from the command line** without touching YubiKey.

🔧 **Step 1: Store OTP Secret in Slot 2**

```bash
ykman otp static --generate 2 --length 32
```

🔧 **Step 2: Script OTP Input with Auto-Type**

```bash
#!/bin/bash
echo "Generating OTP..."
ykman otp code 2 | xclip -selection clipboard
notify-send "OTP Copied!"
```

🚀 **Now, run `./otp.sh` to auto-copy an OTP to the clipboard!**

---

### **🔹 Auto-Login to Websites with YubiKey (No Typing Required)**

🔹 **Goal:** Use YubiKey **to fill in usernames & passwords automatically**.

🔧 **Step 1: Store Credentials in YubiKey**

1. Open **YubiKey Manager** → Go to **OTP Mode**.
2. **Program Slot 2** with:
    - Username + `TAB`
    - Password + `ENTER`

🔧 **Step 2: Auto-Fill Login Fields**

1. Place cursor in **username field**.
2. Touch **YubiKey** → It auto-fills credentials!

---

## **🔹 4️⃣ YubiKey Key Duplication & Backup Strategies**

### **🔹 Duplicating YubiKeys for Redundancy (Prevent Lockout)**

🔹 **Goal:** Set up a **backup YubiKey** in case you lose your primary one.

🔧 **Step 1: Backup PGP & SSH Keys to a Second YubiKey**

```bash
gpg --export-secret-keys YOURKEYID > backup-key.gpg
gpg --export-ssh-key YOURKEYID > backup-ssh.pub
```

🔧 **Step 2: Restore Keys to Backup YubiKey**

```bash
gpg --import backup-key.gpg
gpg --card-edit
gpg/card> keytocard
```

🚀 Now, **both YubiKeys work identically** for SSH, GPG, and authentication!

---

### **🔹 Backup & Restore FIDO2 / U2F Keys (For Multi-Key Authentication)**

🔹 **Goal:** **Duplicate FIDO2 credentials** across multiple YubiKeys.

🔧 **Step 1: Register Multiple YubiKeys for FIDO2 Authentication**

1. Open **Google, GitHub, or Bitwarden security settings**.
2. Register **two YubiKeys** instead of one.
3. Store your backup YubiKey in a **safe location**.

Now, **if one YubiKey is lost, the backup still works!**
