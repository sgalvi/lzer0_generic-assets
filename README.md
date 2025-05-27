# Resource Customization Guide

---

This repository contains generic configurations and scripts designed to be **personalized and integrated into your project**. Follow the steps below to adapt them to your needs.

---

## 🔒 UFW Rules (Uncomplicated Firewall)

UFW configurations are located in `ansible/playbooks/resources/ufw/`. I recommend reviewing and customizing the following files:

- **General UFW service configuration**:
    ```bash
    vi ufw.conf
    ```

- **Specific IPv4 firewall rules**:
    ```bash
    vi user.rules
    ```

- **Specific IPv6 firewall rules**:
    ```bash
    vi user6.rules
    ```

Ensure these rules reflect the ports and services you intend to expose on your system.

---

## 🔑 SSH Keys for Ansible

SSH keys are fundamental for Ansible's operation. They are located in the `pi-gen-conf/key/` directory.

### Key Generation

If you don't already have an SSH key pair, you can generate a new one using the following command:

```bash
ssh-keygen -t ed25519 -C "your_comment_or_email"
```

This will create two files: `id_ed25519` (private key) and `id_ed25519.pub` (public key). Keep your private key **secure** and do not share it.

---

## ⚙️ `custom.toml` File Configuration

The `pi-gen-conf/stage2/90-crspi/custom.toml` file is the heart of the customization. Open it to modify:

```bash
vi pi-gen-conf/stage2/90-crspi/custom.toml
```

### 🔐 User Password Encryption

It is **strongly recommended** to encrypt the user's password before inserting it into the `custom.toml` file. This significantly enhances security.

1. **Encrypt your password**:

    ```bash
    cleartext=your_password_here
    openssl passwd -5 $cleartext
    ```
    The command will return an encrypted string.

2. **Paste the encrypted password**:

    Copy the generated string and paste it into the `password` field within the `[user]` section. Make sure `password_encrypted` is set to `true`.
  
    ```toml
    [user]
    # ... other user configurations
    password = "your_encrypted_password_here"
    password_encrypted = true
    ```

### 🗝️ Authorized Keys

This step is **crucial for Ansible's operation**. You must insert the public key that Ansible will use to connect to your devices.

1. **View your Ansible public key**:

    ```bash
    cat pi-gen-conf/key/id_ed25519.pub
    ```

    Copy the entire content of this key.

2. **Paste into `custom.toml`**:

    Add the copied key to the `authorized_keys` array in the `[ssh]` section.

    ```toml
    [ssh]
    # ... other SSH configurations
    authorized_keys = [ "ssh-ed25519 AAA.../wymcrm your@example.com" ]
    ```

    If you have multiple keys, you can add them separated by commas.

**Security Note**: While it is possible to enable `password_authentication` to `true` for SSH login without keys, this is **discouraged** for security reasons. Key-based authentication is much more robust.


---

## 📂 Adding Public Keys to Devices (Ansible Resources)

To allow SSH access to your devices from other machines (e.g., your development computer or other servers), you need to add their public keys to the `authorized_keys` file dedicated to resources.

- **Add the keys**: Paste the public keys of the devices from which you want to connect via SSH into the file `ansible/playbooks/resources/authorized_keys`.
    
    **IMPORTANT**: Make sure to also include Ansible's SSH key (`id_ed25519.pub` generated previously) in this file, otherwise Ansible will not be able to connect!
