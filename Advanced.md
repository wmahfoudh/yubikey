# Advanced

## **🔹 Phase 4: Advanced YubiKey Configurations & Security Considerations**

At this point, you understand the cryptographic foundations and practical applications of your **YubiKey 5C**. Now, we will explore **advanced configurations**, **security best practices**, and **troubleshooting**.

---

## **🔹 Advanced Configurations**

### **1️⃣ Configuring Multiple Security Modes**

Your YubiKey supports multiple security features **simultaneously**. However, some features may conflict, so configuring them properly is crucial.

🔹 **Check enabled interfaces**:

```sh
ykman config list
```

🔹 **Enable/disable specific functions**:  
Example: Enable FIDO2 and Smart Card (PIV) but disable OTP:

```sh
ykman config usb --enable FIDO2,PIV --disable OTP
```

**Why do this?**

- If you don’t use OTP, disable it to **reduce attack surface**.
- If you only use FIDO2, disabling unnecessary features improves security.

---

### **2️⃣ Using YubiKey for Full Disk Encryption (FDE)**

Your **YubiKey can store cryptographic material** for **LUKS disk encryption** on Linux.

🔹 **Steps to Set Up YubiKey for LUKS**:

1. Install dependencies:
    
    ```sh
    sudo pacman -S yubikey-luks
    ```
    
2. Add a **YubiKey-secured slot** to LUKS:
    
    ```sh
    sudo cryptsetup luksAddKey /dev/sdX --key-file <(ykchalresp -2)
    ```
    
3. At boot, insert **YubiKey** to decrypt the disk.

**Why use this?**

- Your **LUKS key won’t be stored on disk**.
- Attackers need **both your YubiKey and passphrase** to unlock the system.

---

### **3️⃣ Protecting Your SSH Keys with Touch Authentication**

By default, your **YubiKey** acts as a smart card for **SSH authentication**. However, attackers who gain remote access to your machine **could use your key without your knowledge**.

🔹 **Enable touch requirement for SSH authentication**:

```sh
ykman piv set-touch-policy 9a always
```

Now, your **YubiKey requires physical touch** before signing an SSH authentication request.

**Why enable this?**

- Prevents **remote attackers** from misusing your SSH key.
- Ensures only **you** can approve SSH logins.

---

### **4️⃣ Using YubiKey for Windows Login**

If you use **Windows**, you can configure your **YubiKey as a Smart Card** to log in securely.

🔹 **Steps for Windows Login with YubiKey PIV**:

1. Install **Yubico PIV Manager**.
2. Enroll your **Windows account** in **Smart Card authentication**.
3. Insert **YubiKey at login** instead of entering a password.

**Why use this?**

- Replaces **weak passwords** with **hardware-backed authentication**.
- Prevents **keyloggers** from capturing your credentials.

---

## **🔹 Security Considerations & Threat Modeling**

Owning **multiple YubiKeys** means you can set up a **redundancy strategy** to protect against **device failure, theft, or loss**.

### **1️⃣ Creating a Backup YubiKey**

Your **primary YubiKey** should have a **backup counterpart** in case it gets lost or damaged.

🔹 **Steps to clone a YubiKey for backup**:

1. **Copy PGP keys** to a second YubiKey:
    
    ```sh
    gpg --card-edit
    ```
    
    - Use the `admin` command, then `keytocard` to copy each key.
2. **Register both YubiKeys for FIDO2 services**.
    
3. Store the **backup YubiKey** securely (e.g., in a safe).
    

**Why do this?**

- Prevent **account lockout** if your YubiKey is lost.
- Have a **secondary device** ready in case of emergency.

---

### **2️⃣ Self-Destruct & Bricking Protection**

If your YubiKey is **stolen**, you should **immediately revoke its credentials**.

🔹 **Steps to wipe a lost YubiKey** remotely:

1. **Revoke your GPG keys**:
    
    ```sh
    gpg --gen-revoke <your-key-id>
    ```
    
2. **Disable your YubiKey on all FIDO2 accounts** (Google, GitHub, etc.).
3. **If you recover it**, factory reset the device:
    
    ```sh
    ykman reset
    ```
    

**Why do this?**

- Prevents **attackers from using your YubiKey** even if they gain physical possession.
- Ensures **lost keys don’t compromise your accounts**.

---

### **3️⃣ Phishing & MITM Attacks Against YubiKey**

Although **FIDO2 is resistant to phishing**, attackers may still attempt **man-in-the-middle (MITM) attacks**.

#### **How to Protect Yourself**

✅ **Always verify the website’s URL** before inserting your YubiKey.  
✅ **Use Passkeys (WebAuthn) instead of passwords** whenever possible.  
✅ **Enable PIN protection for FIDO2**:

```sh
ykman fido set-pin
```

✅ **Never insert your YubiKey into untrusted computers**.

---

## **🔹 Phase 5: Troubleshooting & Maintenance**

### **1️⃣ Resetting a YubiKey**

If you **forget your PIN** or need to **reset everything**, you can **wipe the YubiKey**:

```sh
ykman reset
```

⚠️ **Warning**: This will erase **all keys, OTPs, and FIDO2 registrations**.

---

### **2️⃣ Fixing GPG Card Not Detected**

If GPG does not recognize your YubiKey, restart the service:

```sh
gpgconf --kill gpg-agent
gpg --card-status
```

---

### **3️⃣ Debugging FIDO2 Issues**

Check FIDO2 logs:

```sh
journalctl -u systemd-udevd | grep -i yubikey
```

---

## **🔹 Final Thoughts & Action Plan**

### **What We've Seen**

✅ The cryptographic foundations of **YubiKey technologies**.  
✅ How to use **FIDO2, OpenPGP, OTP, and Smart Card authentication**.  
✅ Advanced **security configurations** to harden your YubiKey usage.  
✅ **Threat modeling** to prevent loss, phishing, and MITM attacks.  
✅ **Troubleshooting techniques** for common issues.
