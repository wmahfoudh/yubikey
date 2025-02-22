# **🔹 Mastering YubiKey Duplication, Backup, and Recovery**

_How to protect against YubiKey loss, duplication strategies, and disaster recovery_

---

## **📌 Why You Should Care About Backup Strategies**

You now fully understand how **YubiKeys secure your authentication and encryption workflows**. However, you also recognize a major risk:

🚨 **If you lose your YubiKey, you might lose access to everything!** 🚨

Since you've purchased **four YubiKeys**, you have **the perfect setup to create redundancy** while maintaining security. This lesson will cover **everything you need to know about YubiKey duplication, backups, and recovery**.

---

## **🔹 Phase 1: Understanding YubiKey Duplication & Backup Methods**

Since **YubiKeys are hardware-based**, **you cannot simply “copy” one to another** like a USB drive. Instead, we need **strategy-specific duplication methods** for each functionality.

### **📜 Backup vs. Duplication – What's the Difference?**

|**Method**|**Can be Duplicated?**|**How It Works?**|
|---|---|---|
|**FIDO2 (WebAuthn, U2F)**|❌ NO|You must manually add each YubiKey as a security key to accounts.|
|**PIV (Smart Card, SSH Keys)**|✅ YES|Generate identical keys on multiple YubiKeys.|
|**OpenPGP (GPG Encryption, Signing)**|✅ YES|Generate a master key and load subkeys onto multiple YubiKeys.|
|**TOTP (Authenticator Codes)**|✅ YES|Store the same TOTP secrets on multiple YubiKeys.|
|**Static Passwords**|✅ YES|Manually program the same password on multiple YubiKeys.|
|**HOTP / Challenge-Response (LUKS, KeepassXC)**|✅ YES|Manually copy the challenge-response secrets.|

Each function requires **different strategies for duplication**. Now, let’s explore **each method** in detail.

---

## **🔹 Phase 2: Full Duplication Methods**

### **1️⃣ Duplicating FIDO2 / WebAuthn Credentials (Account Logins)**

**Can you clone a FIDO2 key?** ❌ NO  
**Why?** Each YubiKey generates **unique asymmetric keys** during registration.

**Solution:**  
✅ **Register multiple YubiKeys** for each service you use.

#### **Step-by-Step: Adding Backup YubiKeys for FIDO2**

1️⃣ **Go to the website's security settings** (Google, GitHub, Microsoft, etc.).  
2️⃣ **Add a second (or third, fourth) YubiKey** as an additional security key.  
3️⃣ **Store your backup YubiKeys securely.**

---

### **2️⃣ Duplicating PIV (Smart Card, SSH, Windows Login)**

✅ **YES – You can copy PIV certificates and keys**.

#### **Method 1: Generate Identical SSH Keys on Multiple YubiKeys**

1️⃣ **Generate a key on the primary YubiKey**:

```sh
ykman piv keys generate --algorithm RSA2048 9a primary-pubkey.pem
```

2️⃣ **Export the public key**:

```sh
ykman piv certificates export 9a primary-cert.pem
```

3️⃣ **Import the key and certificate into a backup YubiKey**:

```sh
ykman piv keys import 9a primary-key.pem
ykman piv certificates import 9a primary-cert.pem
```

✅ **Now both YubiKeys have identical keys for SSH authentication.**

---

### **3️⃣ Duplicating OpenPGP (GPG Encryption, Signing, SSH)**

✅ **YES – You can duplicate your GPG keys across multiple YubiKeys**.

#### **Step-by-Step: Backup Your GPG Key to a Second YubiKey**

1️⃣ **Export your GPG secret key**:

```sh
gpg --export-secret-keys --armor > master-key.asc
```

2️⃣ **Import it into the second YubiKey**:

```sh
gpg --import master-key.asc
gpg --edit-key <YOUR_KEY_ID>
```

3️⃣ **Move the key to the second YubiKey**:

```sh
keytocard
```

✅ **Now both YubiKeys have identical GPG encryption and signing keys.**

---

### **4️⃣ Duplicating TOTP (Google Authenticator, 2FA Codes)**

✅ **YES – You can manually copy TOTP secrets to multiple YubiKeys.**

#### **Step-by-Step: Store the Same TOTP Secret on Multiple YubiKeys**

1️⃣ **Extract the TOTP secret from a service (Google, GitHub, etc.).**  
2️⃣ **Manually add it to both YubiKeys**:

```sh
ykman oath accounts add my-service --secret <TOTP_SECRET>
```

✅ **Now both YubiKeys generate identical 2FA codes.**

---

### **5️⃣ Duplicating Challenge-Response for LUKS / KeepassXC**

✅ **YES – You can manually copy Challenge-Response secrets.**

#### **Step-by-Step: Store the Same Secret on Multiple YubiKeys**

1️⃣ **Generate a Challenge-Response secret on the primary YubiKey**:

```sh
ykman otp chalresp --touch --generate 2
```

2️⃣ **Copy the secret to a second YubiKey**:

```sh
ykman otp chalresp --touch 2
Enter a secret key: <PASTE_THE_SAME_SECRET_FROM_STEP_1>
```

✅ **Now both YubiKeys generate identical Challenge-Response hashes.**

---

## **🔹 Phase 3: Resetting & Restoring YubiKeys**

If a YubiKey is lost, stolen, or compromised, you might need to **reset** it.

### **1️⃣ Reset the Entire YubiKey (Factory Reset)**

🚨 **Warning: This erases EVERYTHING** 🚨

```sh
ykman reset
```

---

### **2️⃣ Reset Individual Components**

✅ **Reset OpenPGP** (Deletes GPG keys)

```sh
ykman openpgp reset
```

✅ **Reset PIV Smart Card**

```sh
ykman piv reset
```

✅ **Reset FIDO2 Credentials**

```sh
ykman fido reset
```

✅ **Reset OTP (Yubico OTP, TOTP, Challenge-Response)**

```sh
ykman otp delete 1
ykman otp delete 2
```

---

## **🔹 Phase 4: Advanced Security & Disaster Recovery**

### **1️⃣ What Happens If You Lose All Your YubiKeys?**

🚨 **Disaster Scenario**: All YubiKeys are lost. How do you regain access?

**Solution:**  
✅ **Backup your GPG keys** on an offline device.  
✅ **Store TOTP secrets in an encrypted vault (e.g., KeePassXC).**  
✅ **Keep a list of recovery codes for FIDO2 accounts.**
