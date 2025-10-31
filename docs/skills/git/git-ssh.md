---
draft: false
date: 2025-10-12
description: >
  Generate and Configure a Git SSH Key
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Generate and Configure a Git SSH key

You can connect to GitHub using either **HTTPS** or **SSH**.
Both methods are secure, but they differ in how they handle authentication:

- **HTTPS** uses a **personal access token (PAT)** or your GitHub credentials, which can be stored securely using a credential manager (e.g., Windows Credential Manager, macOS Keychain, or Git Credential Helper on Linux).

- **SSH** uses a **pair of cryptographic keys** (private and public) for authentication, eliminating the need for credentials or tokens once configured.

For most developers, **SSH is preferred** because it provides a simple, password-free workflow and makes it easy to manage multiple GitHub accounts or devices.

## 1. Generate an SSH key

To generate a new SSH key pair, run:

```bash
cd ~/.ssh
ssh-keygen -t ed25519 -C "your_email@example.com"
```

If the `~/.ssh` directory doesn't exist, create it first:

```bash
mkdir ~/.ssh
```

When prompted:

- Use a descriptive filename, for example: `id_ed25519_github`
- Enter a **secure passpharase** (recommended)

## 2. Add the SSH key to GitHub

Once the key pair is generated, display your **public key**:

```bash
cat id_ed25519_github.pub
```

Copy the output and add it to your GitHub account:

Then in your GitHub page:

1. Go to **[GitHub -> Settings -> SSH and GPG keys](https://github.com/settings/keys)**
2. **Click New SSH key**
3. Fill the fields as follows:
    - **Title**: a short label for your device (e.g., workstation-linux, macbook-personal)
    - **Key Type**: Authentication Key
    - **Key**: paste the copied public key
4. Click **Add SSH key** to save it

## 3. Configure SSH for GitHub

Tell SSH which key to use when connection to GitHub by editing or creating
the file `~/.ssh/config`:

```bash
# GitHub account
Host github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519_github
```

To test the connection:

```bash
ssh -T git@github.com
```

If everything is configured correctly, you should see a message like:

```plain
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```

## Reference Material

**Git**

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Reference](https://git-scm.com/docs)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)

**GitHub**

- [Generating a new SSH key adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)