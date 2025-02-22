# 🔹 Additional YubiKey Functionalities & Expansions

After reviewing the article, I identified the following **new concepts** that require deeper explanation:

1. **Static Password on Touch** – Using YubiKey as a **HID keyboard** to input a stored password.
2. **Securing Keepass Database** – Challenge-response authentication for KeepassXC.
3. **Modern File Encryption with `age`** – Encrypting files using YubiKey-backed identities.
4. **Advanced PIV Usage**
    - **PINs for PIV** – Understanding the PIN, PUK, and Management Key in PIV.
    - **Importing an existing SSH Key into YubiKey PIV**.
5. **Using YubiKey for Full Disk Encryption (LUKS 2FA)** – Protecting **LUKS-encrypted** disks with YubiKey challenge-response authentication.

Now, let’s go **deeper into each topic**.

---

## **1️⃣ Static Password on Touch – Using YubiKey as a Secure HID Device**

### **🔹 What is it?**

The **Static Password** feature turns your YubiKey into a **USB keyboard** (HID - Human Interface Device). When you touch it, it automatically types a **pre-stored password**.

This is useful for **logging into legacy systems** that don’t support FIDO2 or OTP-based 2FA.

### **🔹 How it Works**

YubiKey has **two slots** for static passwords:

- **Slot 1 (Short Touch)** – Triggered by a quick tap.
- **Slot 2 (Long Touch)** – Triggered by holding down the YubiKey.

### **🔹 How to Configure It on Arch Linux**

**Set a custom static password for Slot 1:**

```sh
ykman otp static --keyboard-layout us 1
Enter a static password: MySecurePassw0rd!
```

**Generate a random 32-character password in Slot 2:**

```sh
ykman otp static --generate 2 --length 32
```

**Check the slot configurations:**

```sh
ykman otp info
```

**Delete a static password from Slot 1:**

```sh
ykman otp delete 1
```

### **🔹 Security Considerations**

❌ **Weakness:** Static passwords are vulnerable to **keyloggers**. If an attacker records all keystrokes, they capture the **full password**.  
✅ **Mitigation:** Combine it with **another factor** (e.g., a manually typed prefix). Example:

- **Stored in YubiKey:** `b7$42gf!@Q`
- **Manually typed first:** `MySuperSecurePass`
- Final Password: `MySuperSecurePassb7$42gf!@Q`

---

## **2️⃣ Securing KeepassXC Database with YubiKey**

### **🔹 What is it?**

Instead of **just using a password** for **KeepassXC**, you can **bind it to your YubiKey** using the **Challenge-Response** authentication method.

This means:

- Even if an attacker **steals your password database**, they **cannot decrypt it** without your YubiKey.

### **🔹 How it Works**

1. **KeepassXC sends a challenge** to the YubiKey.
2. **YubiKey processes the challenge** using its internal **HMAC-SHA1** function and **returns a response**.
3. **KeepassXC verifies the response** to grant access.

### **🔹 How to Configure on Arch Linux**

1️⃣ **Enable Challenge-Response in YubiKey**:

```sh
ykman otp chalresp --touch --generate 2
```

This generates a **random secret key** for **Slot 2**.

2️⃣ **Pair with KeepassXC**:

- Open **KeepassXC** → `Database > Database Security > Add Challenge-Response`
- Select **YubiKey Slot 2** and click **Refresh**

3️⃣ **Verify that it works:**

```sh
ykman otp chalresp --touch 2
```

If configured correctly, your YubiKey should respond with a **HMAC-SHA1** code.

### **🔹 Security Considerations**

❌ **Not true 2FA** – Since the challenge is **always the same**, an attacker who captures a **USB traffic log** could replay the response.  
✅ **Mitigation** – Store a **backup YubiKey** with the same challenge-response secret.

---

## **3️⃣ Modern File Encryption with `age` (age-plugin-yubikey)**

### **🔹 What is it?**

**`age` (Actually Good Encryption)** is a **modern alternative to GPG** for encrypting files. **`age-plugin-yubikey`** allows you to **store private keys inside your YubiKey**.

**Key benefits:**  
✅ **Your private key never leaves the YubiKey**  
✅ **No need to remember passwords**  
✅ **Touch-to-decrypt security**

### **🔹 How to Encrypt Files with YubiKey `age` Plugin**

1️⃣ **Install `age` and `age-plugin-yubikey` on Arch Linux:**

```sh
sudo pacman -S age age-plugin-yubikey
```

2️⃣ **Generate an encryption identity:**

```sh
age-plugin-yubikey --generate --name my-encryption-key
```

This **stores the encryption key** inside the YubiKey.

3️⃣ **Encrypt a file using the YubiKey identity:**

```sh
cat secret.txt | age -r age1yubikey1q228ff7yq... > secret.txt.age
```

4️⃣ **Decrypt the file (requires YubiKey touch):**

```sh
cat secret.txt.age | age -d -i identity.txt > secret.txt
```

### **🔹 Security Considerations**

❌ **Lost YubiKey = Lost Files** – If you lose your YubiKey, you lose **all encrypted data**.  
✅ **Mitigation** – Encrypt each file **twice**, using two **different YubiKeys**.

---

## **4️⃣ Advanced PIV Usage**

### **🔹 PINs for PIV: How It Works**

When using YubiKey PIV **Smart Card Authentication**, there are **three security layers**:

1. **PIN (6 digits)** – Used to authenticate users.
2. **PUK (8 digits)** – Used to reset the PIN if forgotten.
3. **Management Key (24 bytes)** – Used for administrative tasks (key generation, importing certificates).

**Reset PIV PIN if you forget it:**

```sh
ykman piv access unblock-pin
```

**Reset entire PIV module to factory defaults:**

```sh
ykman piv reset
```

---

## **5️⃣ Using YubiKey for LUKS Full Disk Encryption (2FA)**

### **🔹 What is it?**

Instead of **just using a passphrase** to unlock your LUKS-encrypted disk, you can **bind it to your YubiKey** for **challenge-response authentication**.

**How It Works:**

1. During boot, LUKS sends a **challenge** to the YubiKey.
2. YubiKey processes the challenge and **returns a response**.
3. LUKS verifies the response and **unlocks the disk**.

### **🔹 How to Configure on Arch Linux**

1️⃣ **Set up challenge-response authentication in Slot 2:**

```sh
ykman otp chalresp --touch --generate 2
```

2️⃣ **Configure LUKS to require YubiKey during boot:**

```sh
ykfde-enroll /dev/sdX
```

3️⃣ **At boot, unlock the disk by touching the YubiKey.**

### **🔹 Security Considerations**

❌ **If you lose the YubiKey, you lose access to the disk.**  
✅ **Mitigation** – Keep a **backup challenge-response secret** stored securely.
