---
title: GitHub SSH Setup for Two-Factor Authentication
tags:
  - hands-on
  - mgmt54500
parent: "[[SysDev Hands-On 8.1 - Hello World API]]"
---

# GitHub SSH Setup for Two-Factor Authentication

If you have two-factor authentication (2FA) enabled on your GitHub account, you've probably seen an error like this when trying to push:

```
remote: Support for password authentication was removed on August 13, 2021.
fatal: Authentication failed for 'https://github.com/...'
```

This happens because GitHub no longer accepts your password over HTTPS when 2FA is enabled. The fix is to switch from HTTPS to SSH — a more secure authentication method that uses a key pair instead of a password. Once it's set up, pushing to GitHub works without any extra prompts.

Work through these steps in order, then return to **Part 10** of the main exercise to commit and push.

---

## Part A: Generate an SSH Key in Cloud Shell

An SSH key pair consists of two files: a **private key** (stays on your machine) and a **public key** (shared with GitHub). Think of the public key as a lock you give to GitHub, and the private key as the only key that opens it.

In your **Cloud Shell terminal**, run:

```bash
ssh-keygen -t ed25519 -C "your@email.com"
```

Replace `your@email.com` with the email address on your GitHub account.

You'll see a few prompts:

1. **Enter file in which to save the key:** Press `Enter` to accept the default location (`~/.ssh/id_ed25519`). _Don't change this unless you know what you're doing._
2. **Enter passphrase:** You can set a passphrase for extra security, or press `Enter` twice to skip it. Either choice works for this course.

When it finishes, you'll see a confirmation message and a small piece of ASCII art. That means the key pair was created successfully.

Verify the files exist by entering the following on the command line:

```bash
ls ~/.ssh
```

You should see at least `id_ed25519` (your private key) and `id_ed25519.pub` (your public key).

> [!IMPORTANT]
> **Never share your private key.** The `.pub` file is the only one you'll copy anywhere. The private key file (`id_ed25519`, no extension) stays on your machine and never leaves it.

---

## Part B: Copy Your Public Key

Print the contents of your public key to the terminal:

```bash
cat ~/.ssh/id_ed25519.pub
```

You'll see a single long line of text starting with `ssh-ed25519` and ending with your email address. It will look something like:

```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAA... your@email.com
```

Select the entire line and copy it to your clipboard. You need all of it — don't cut off either end!!

> [!TIP] 
> In Cloud Shell, you can click and drag to select text, then use `Ctrl+C` to copy it (or right-click → Copy if that doesn't work).

---

## Part C: Add the Public Key to Your GitHub Account

1. Go to [github.com](https://github.com) and sign in
2. Click your **profile photo** in the top right corner and select **Settings**
3. In the left sidebar, click **SSH and GPG keys**
4. Click the green **New SSH key** button
5. In the **Title** field, enter something descriptive like `Purdue GCP Cloud Shell`
6. Leave **Key type** set to **Authentication Key**
7. In the **Key** field, paste the public key you copied in Part B
8. Click **Add SSH key**

GitHub will ask you to confirm with your password or 2FA code. After confirming, you'll see your new key listed on the SSH keys page.

> [!NOTE]
> **Why a descriptive title?** If you ever set up Cloud Shell again — or use a different machine — you can add another key without confusion. Titles help you track which key belongs to which environment.

---

## Part D: Test the SSH Connection

Before updating your repository, verify that Cloud Shell can actually reach GitHub over SSH:

```bash
ssh -T git@github.com
```

The first time you connect to a new host, SSH will ask you to confirm its identity:

```
The authenticity of host 'github.com (...)' can't be established.
Are you sure you want to continue connecting (yes/no/[fingerprint])?
```

Type `yes` and press `Enter`. You should then see:

```
Hi your-username! You've successfully authenticated, but GitHub does not provide shell access.
```

That message is expected — it means authentication worked. GitHub is confirming your identity but letting you know you can't open an interactive shell session (which you don't need to).

If you see `Permission denied (publickey)` instead, double-check that you copied the full contents of `id_ed25519.pub` and that you saved the key in GitHub without any extra spaces or line breaks.

---

## Part E: Update Your Repository's Remote URL

Your repository is currently configured to use HTTPS. Now that SSH is set up, you need to switch it to the SSH URL. The two formats look like this:

- **HTTPS:** `https://github.com/your-username/hello-world-api.git`
- **SSH:** `git@github.com:your-username/hello-world-api.git`

First, confirm what remote URL you're currently using:

```bash
git remote -v
```

You'll see output like:

```
origin  https://github.com/your-username/hello-world-api.git (fetch)
origin  https://github.com/your-username/hello-world-api.git (push)
```

Remove the existing remote:

```bash
git remote remove origin
```

## Obtain the SSH URL to Your Repository
Visit your repository in GitHub.

Click the green **Code** button and copy the **SSH** URL — you'll need it shortly. It will look like:

```
git@github.com:ree/hello-world-api.git
```

## Update Your Repository URL

In **Google Cloud Shell**, open the terminal window and ensure you are within your `hello-world-api` directory. Then enter the following:

```bash
git remote add origin {SSH URL}`
```

**PASTE** the URL you copied in the previous step. For example, my command would be

_git remote add origin git@github.com:ree\/hello-world-api.git_

---

Confirm the change took effect:

```bash
git remote -v
```

You should now see `git@github.com:...` instead of `https://github.com/...`.

> **Where do I find my SSH URL?** Go to your repository on GitHub, click the green **Code** button, and select the **SSH** tab. Copy the URL from there — it starts with `git@github.com:`.

---

## ✅ You're Ready When...

- [ ] `ssh-keygen` created `id_ed25519` and `id_ed25519.pub` in `~/.ssh`
- [ ] Your public key is saved in your GitHub account under **SSH and GPG keys**
- [ ] `ssh -T git@github.com` returns a success message with your username
- [ ] `git remote -v` shows an SSH URL (`git@github.com:...`), not an HTTPS URL

Once all four are checked off, head back to [**Part 10** of the main exercise](README.md) to commit and push your code.