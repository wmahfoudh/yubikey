# **🔹 Advanced YubiKey Configuration & Troubleshooting Guide**

_How to solve common issues, optimize security, and automate YubiKey usage_

---

## **📌 Why Advanced YubiKey Configuration Matters**

While YubiKey **works out of the box**, advanced users can unlock **hidden features, automate tasks, and integrate it into high-security environments**. This guide covers:

✅ **Fixing common YubiKey issues (connectivity, driver problems, reset methods)**  
✅ **Advanced configurations (customizing slots, setting PIN policies, key rotation)**  
✅ **Automating YubiKey usage for faster authentication**  
✅ **Troubleshooting SSH, GPG, OpenVPN, and FIDO2 issues**

Let’s dive in. 🔐

---

## **🔹 Troubleshooting Common YubiKey Issues**

### **1️⃣ YubiKey Not Detected (Linux, Windows, macOS)**

🔍 **Symptoms:**

- `ykman list` or `gpg --card-status` doesn’t show the YubiKey.
- Not recognized in browsers or authentication dialogs.

🔧 **Fixes:**  
✅ **Check USB connection** – Try a different **USB port** or **USB-C adapter**.  
✅ **Restart `pcscd` (Linux & macOS)** – Some features (PIV, GPG) require it:

```bash
sudo systemctl restart pcscd
```

✅ **Check device logs (Linux)** – Run:

```bash
dmesg | grep -i yubikey
```

✅ **Reinstall drivers (Windows)** – Update **YubiKey Manager, OpenSC, and GPG4Win**.

---

### **2️⃣ YubiKey FIDO2 / U2F Not Working in Browser**

🔍 **Symptoms:**

- YubiKey **doesn’t register on websites** using FIDO2/WebAuthn.
- **Chrome, Firefox, Edge** don’t detect it.

🔧 **Fixes:**  
✅ **Check browser settings** – Ensure U2F/FIDO2 is enabled:

- **Chrome:** `chrome://flags/#enable-webauthn` → Enable WebAuthn
- **Firefox:** `about:config` → Set `security.webauth.u2f` to `true`

✅ **Test YubiKey FIDO2 functionality** – Use Yubico’s test page:  
[https://demo.yubico.com/webauthn-technical](https://demo.yubico.com/webauthn-technical)

✅ **Enable U2F manually in Arch Linux (for Firefox/Snap users)**

```bash
sudo udevadm control --reload-rules && sudo udevadm trigger
```

---

### **3️⃣ Resetting a YubiKey (Factory Reset)**

🔍 **Symptoms:**

- **Forgot PIN/PUK?** Locked out of **PIV, GPG, or OTP slots?**
- Want to completely **wipe and start fresh**?

🔧 **Fixes:**  
✅ **Reset PIV (Smartcard) module**

```bash
ykman piv reset
```

✅ **Reset FIDO2 module**

```bash
ykman fido reset
```

✅ **Reset OTP slots (if misconfigured)**

```bash
ykman otp delete 1
ykman otp delete 2
```

✅ **Reset OpenPGP keys**

```bash
gpg --card-edit
gpg/card> admin
gpg/card> factory-reset
```

---

## **🔹 Advanced YubiKey Configurations**

### **1️⃣ Customizing OTP & Static Password Slots**

YubiKey supports **two programmable slots**, each with different functions:  
✅ **Slot 1** – Short press (default: Yubico OTP).  
✅ **Slot 2** – Long press (used for static passwords, challenge-response, or custom OTP).

🔹 **Set a static password (e.g., master password for KeePass)**

```bash
ykman otp static --generate 2 --length 32
```

🔹 **Store a custom OTP in Slot 1**

```bash
ykman otp program 1 -o append-cr -o fixed 6 -o generate 32
```

🔹 **Swap slot assignments**

```bash
ykman otp swap
```

---

### **2️⃣ Automating YubiKey for Faster Authentication (Linux & Windows)**

You can **automate YubiKey input** to speed up authentication processes.

✅ **Auto-type static password in Linux**

1. Create a udev rule to auto-trigger OTP on insert:
    
    ```bash
    echo 'ACTION=="add", ATTRS{idVendor}=="1050", ATTRS{idProduct}=="0407", RUN+="/usr/bin/ykman otp code 1 | xclip -selection clipboard"' | sudo tee /etc/udev/rules.d/99-yubikey.rules
    ```
    
2. Restart udev:
    
    ```bash
    sudo udevadm control --reload-rules && sudo udevadm trigger
    ```
    

Now, **when you insert your YubiKey, it auto-copies an OTP to your clipboard**.

✅ **Auto-login with Windows PowerShell**

1. Open PowerShell and enter:
    
    ```powershell
    Get-Content "C:\yubikey_otp.txt" | Set-Clipboard
    ```
    
2. Press **Ctrl+V** after inserting your YubiKey to paste the OTP.

---

### **3️⃣ Enabling PIN & Touch Policies for Maximum Security**

To enhance security, configure YubiKey to **require a PIN or physical touch** before use.

✅ **Set a PIN for FIDO2 authentication**

```bash
ykman fido access change-pin
```

✅ **Require touch for OpenPGP authentication**

```bash
ykman openpgp keys set-touch aut on
```

✅ **Require touch for PIV (Smartcard authentication)**

```bash
ykman piv keys set-touch-policy 9a always
```

---

## **🔹 Troubleshooting YubiKey with SSH, GPG, & VPNs**

### **1️⃣ YubiKey Not Working with SSH Authentication (Linux & Windows)**

🔍 **Symptoms:**

- `ssh-add -L` doesn’t show YubiKey keys.
- SSH authentication **fails with “no keys found” error**.

🔧 **Fixes:**  
✅ **Restart gpg-agent & enable SSH support**

```bash
gpgconf --kill gpg-agent
export SSH_AUTH_SOCK=$(gpgconf --list-dirs agent-ssh-socket)
```

✅ **Ensure your public key is added to `~/.ssh/authorized_keys`**

```bash
ssh-keygen -D /usr/lib64/pkcs11/opensc-pkcs11.so >> ~/.ssh/authorized_keys
```

---

### **2️⃣ YubiKey Failing with GPG Smartcard (Incorrect PIN or Key Not Found)**

🔍 **Symptoms:**

- `gpg --card-status` shows no keys.
- **Incorrect PIN errors, even after entering the correct one**.

🔧 **Fixes:**  
✅ **Reset the GPG agent**

```bash
gpgconf --kill gpg-agent && gpg --card-status
```

✅ **Increase PIN retry attempts**

```bash
ykman piv access set-retries 5 5
```

---

### **3️⃣ Using YubiKey for OpenVPN Authentication**

1. **Enable PKCS#11 support** in OpenVPN:
    
    ```bash
    sudo openvpn --show-pkcs11-ids /usr/lib64/pkcs11/opensc-pkcs11.so
    ```
    
2. **Edit OpenVPN config file (`client.ovpn`)**:
    
    ```
    pkcs11-providers /usr/lib64/pkcs11/opensc-pkcs11.so
    pkcs11-id "PIV_II (Yubikey) 01"
    ```
    
3. **Start OpenVPN with YubiKey authentication**:
    
    ```bash
    sudo openvpn --config client.ovpn
    ```
