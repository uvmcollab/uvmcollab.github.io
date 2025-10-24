---
draft: false
date: 2025-10-12
description: >
  Git SSH Key
authors: Ciro Bermudez
icon: material/git
hide: 
#  - navigation
#  - toc
---

# :material-git: Generate an SSH key

## Generate an SSH key

You can interact with GitHub using either **HTTPS** or **SSH**. The **recommended** protocol is SSH,
because it uses a pair of **private/public keys** and does not require password authentication.

To use SSH you first need to [generate a new SSH key pair](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key) with:

```bash
cd ~/.ssh
ssh-keygen -t ed25519 -C "your_email@example.com"
```

- If the `~/.ssh` directory doesn't already exist, create it with:

  ```bash
  mkdir ~/.ssh
  ```

- When prompted for the file name, use a meaningful name such as `id_ed25519_github`.
- Enter a passphrase when asked (recommended for security).

## Add the SSH key to GitHub

Next, [add your SSH **public key**](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) to your GitHub account. 

Display the public key with:

```bash
cat id_ed25519_github.pub
```

Then:

- Click you GitHub profile picture.
- Navigate to **Settings > SSH and GPG keys > New SSH Key**. See your [SSH keys](https://github.com/settings/keys).
- Create a new key entry and paste the public key content.

## Configure SSH for GitHub

Finally create (or edit) the `~/.ssh/config` file to tell SSH which key to use for GitHub:

```bash
# GitHub account
Host github.com
  HostName github.com
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/id_ed25519_github
```

## Reference Material

### Git

- [Pro Git Book](https://git-scm.com/book/en/v2)
- [Git Cheat Sheet](https://git-scm.com/cheat-sheet)

### GitHub

- [Generating a new SSH key adding it to the ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
- [Adding a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)