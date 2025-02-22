# **🔹 Practical YubiKey Implementation with Real Services**

_Step-by-step guide for using YubiKey with various services across different platforms_

---

## **📌 Why This Matters**

Now that you understand **how YubiKey works**, it’s time to **implement it in real-world services**.

🛡 **Key Considerations:**  
✔ **Different services support different YubiKey authentication types**.  
✔ **Mobile and desktop authentication work differently**.  
✔ **Backup YubiKeys should be registered to avoid lockout**.

This guide categorizes **popular services** by authentication type, provides **implementation steps**, and explains **cross-device compatibility** (mobile, desktop, browser, CLI).

---

## **🔹 Service Categories by YubiKey Authentication Type**

|**Security Type**|**Service Examples**|**Implementation Complexity**|
|---|---|---|
|**FIDO2 / WebAuthn**|Google, Microsoft, GitHub, Bitwarden, Facebook, Twitter, Dropbox|⭐⭐⭐ Easy|
|**PIV (Smart Card)**|Windows Login, macOS Login, SSH Authentication|⭐⭐⭐⭐ Advanced|
|**OpenPGP (GPG)**|Email encryption (ProtonMail, Thunderbird), Git signing, SSH authentication|⭐⭐⭐⭐ Advanced|
|**TOTP (Authenticator Replacement)**|Google, Facebook, AWS, Bitwarden, PayPal, Reddit|⭐⭐⭐ Easy|
|**Challenge-Response**|LUKS (Full Disk Encryption), KeePassXC|⭐⭐⭐⭐ Advanced|

Let’s go through each category **step by step**.

---

## **🔹 1️⃣ FIDO2 / WebAuthn – Passwordless & 2FA Authentication**

🚀 **Use case:** Secure logins with **biometric-like authentication**, completely eliminating passwords.

### **🛠 Services That Support FIDO2 / WebAuthn**

✅ **Google** (Gmail, YouTube, Google Drive)  
✅ **GitHub, GitLab**  
✅ **Microsoft (Windows Login, Outlook, OneDrive, Xbox)**  
✅ **Bitwarden (Password Manager)**  
✅ **Facebook, Twitter, Dropbox**

---

### **🔹 How to Set Up FIDO2 on GitHub (Example)**

1️⃣ **Go to GitHub Settings** → `Security → Two-Factor Authentication → Security Keys`  
2️⃣ **Insert your YubiKey and touch it** when prompted.  
3️⃣ **Register backup keys** (if available).  
4️⃣ **Save recovery codes** in case of emergency.

---

### **🔹 How FIDO2 Works on Mobile**

📱 **Can I use my YubiKey for FIDO2 logins on my phone?**  
✅ **Yes! But it depends on the phone model & OS.**

|**Device**|**Connection Type for YubiKey**|**Works?**|
|---|---|---|
|**Android**|**NFC** / USB-C|✅ Yes, tap the YubiKey for FIDO2 login|
|**iPhone**|**Lightning (YubiKey 5Ci)** / NFC|✅ Yes, use NFC or insert into the Lightning port|
|**Windows/macOS**|**USB-A, USB-C, Bluetooth (YubiKey Bio)**|✅ Yes, insert YubiKey or use Bluetooth|

**Example: Logging into Bitwarden (FIDO2) on Mobile**

- **Android**: Tap the YubiKey via NFC when prompted.
- **iPhone**: Tap NFC or insert a **YubiKey 5Ci** into the Lightning port.

---

## **🔹 2️⃣ PIV (Smart Card) – Secure Windows, macOS, SSH Logins**

🚀 **Use case:** YubiKey acts as a **physical smart card** to authenticate users.

### **🛠 Services That Support PIV**

✅ **Windows Login (Smart Card Mode)**  
✅ **macOS Login**  
✅ **SSH Authentication (Linux, Windows, macOS)**

---

### **🔹 How to Use YubiKey for Windows Login**

1️⃣ **Enable PIV mode:**

```sh
ykman piv enable
```

2️⃣ **Generate a PIV key for login:**

```sh
ykman piv keys generate 9a pubkey.pem
ykman piv certificates generate 9a pubkey.pem
```

3️⃣ **Import the certificate into Windows:**

- Open **MMC** (`win + R → mmc → Enter`).
- Navigate to **Certificates > Smart Card Login** and import the `.pem` file.
- Enable **Smart Card Login** in Windows settings.

4️⃣ **Restart and log in using the YubiKey.**

---

## **🔹 3️⃣ OpenPGP (GPG) – Secure Email, Git, SSH**

🚀 **Use case:** Securely **sign commits, encrypt emails, and authenticate SSH logins.**

### **🛠 Services That Support OpenPGP**

✅ **ProtonMail, Thunderbird (Email Encryption)**  
✅ **GitHub, GitLab (Commit Signing)**  
✅ **SSH Authentication (Linux, Windows, macOS)**

---

### **🔹 How to Use YubiKey for Git Signing**

1️⃣ **Set up GPG on the YubiKey:**

```sh
gpg --card-edit
admin
keytocard
```

2️⃣ **Configure Git to use YubiKey for signing:**

```sh
git config --global user.signingkey <GPG_KEY_ID>
git commit -S -m "Signed commit"
```

3️⃣ **Verify signatures:**

```sh
git log --show-signature
```

---

## **🔹 4️⃣ TOTP (Authenticator Replacement) – 2FA for Any Website**

🚀 **Use case:** Use YubiKey as a **Google Authenticator replacement**.

### **🛠 Services That Support TOTP**

✅ **Google (Gmail, YouTube, Drive)**  
✅ **Facebook, Twitter, Reddit**  
✅ **AWS, DigitalOcean, PayPal**  
✅ **Bitwarden, 1Password**

---

### **🔹 How to Use YubiKey for Google 2FA (TOTP)**

1️⃣ **Enable OATH-TOTP on the YubiKey:**

```sh
ykman oath add google --secret <BASE32_SECRET>
```

2️⃣ **Generate codes on demand:**

```sh
ykman oath code google
```

📱 **Mobile Usage:** Use **Yubico Authenticator (Android, iOS)** to generate codes.

---

## **🔹 5️⃣ Challenge-Response – Secure KeepassXC & LUKS Disk Encryption**

🚀 **Use case:** YubiKey verifies your identity **before unlocking sensitive data**.

### **🛠 Services That Support Challenge-Response**

✅ **KeePassXC (Password Manager)**  
✅ **LUKS (Full Disk Encryption)**

---

### **🔹 How to Use YubiKey for KeepassXC**

1️⃣ **Enable Challenge-Response mode on Slot 2:**

```sh
ykman otp chalresp --touch --generate 2
```

2️⃣ **Pair YubiKey with KeepassXC:**

- Open KeepassXC → `Database > Database Security > Add Challenge-Response`
- Select **YubiKey Slot 2**
- Save changes and test login

✅ **Now, KeepassXC requires YubiKey for access!**
