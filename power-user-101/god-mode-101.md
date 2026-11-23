# Power User 001 — Developer Laptop Setup Guide

> One guide to set up **Windows, macOS, and Linux** for development. No GUI installers — everything done via terminal with package managers.

---

## Table of Contents

- [What We're Building](#what-were-building)
- [Windows Setup](#windows-setup)
- [macOS Setup](#macos-setup)
- [Linux Setup](#linux-setup)
- [Common Shell Setup (All Platforms)](#common-shell-setup-all-platforms)
- [Verification](#verification)

---

## What We're Building

By the end of this guide, your machine will have:

| Tool | Purpose | Windows | macOS | Linux |
|------|---------|---------|-------|-------|
| Terminal app | Run commands | Windows Terminal | Terminal (built-in) | Terminal (built-in) |
| Package manager | Install dev tools | Chocolatey | Homebrew | apt / dnf / pacman |
| Git | Version control | via Chocolatey | via Homebrew | via apt/dnf/pacman |
| Docker | Containers | via Chocolatey | via Homebrew | via apt |
| Zsh + Oh My Zsh | Better shell | via WSL | native | native |
| zsh-autosuggestions | Smarter autocomplete | plugin | plugin | plugin |

Every command is **copy-paste ready**. You don't need to type anything.

> Reference links to official documentation are included throughout for troubleshooting.

---

## Windows Setup

### Step 1 — Install Windows Terminal

Windows Terminal gives you tabs, panes, and a modern interface (multiple windows/splits).

Open **Microsoft Store**, search "Windows Terminal", click **Install**. Or use winget (built-in):

> Official docs: [learn.microsoft.com/en-us/windows/terminal](https://learn.microsoft.com/en-us/windows/terminal/)

```powershell
winget install --id=Microsoft.WindowsTerminal -e
```

After install, **right-click the Start button → Terminal (Admin)** for all remaining commands.

> All Windows commands below must run in **Windows Terminal as Administrator**.

---

### Step 2 — Install Chocolatey (Package Manager)

Copy and paste the entire block into your admin Terminal:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

> Official docs: [chocolatey.org/install](https://chocolatey.org/install)

Verify it worked:

```powershell
choco --version
```

You should see a version number like `2.x.x.x`.

---

### Step 3 — Install WSL2 (Windows Subsystem for Linux)

WSL2 lets you run a full Linux environment inside Windows — essential for real dev work.

> Official docs: [learn.microsoft.com/en-us/windows/wsl](https://learn.microsoft.com/en-us/windows/wsl/)

```powershell
wsl --install -d Ubuntu
```

After the install finishes, **restart your computer**. When you log back in, Ubuntu will finish setting up — create a Linux username and password when prompted.

On modern Windows, WSL2 is the default. But if needed, set it explicitly:

```powershell
wsl --set-default-version 2
```

To open your Linux environment at any time, run:

```powershell
wsl
```

Or open Windows Terminal and select "Ubuntu" from the dropdown.

---

### Step 4 — Install Git via Chocolatey

Back in **Windows Terminal (Admin)**:

```powershell
choco install git -y
```

> Official docs: [git-scm.com](https://git-scm.com/)

Close and re-open Terminal to pick up the new PATH (or run `refreshenv` in PowerShell — note: `refreshenv` only works in PowerShell, not in classic cmd).

---

### Step 5 — Install Docker via Chocolatey

```powershell
choco install docker-desktop -y
```

> Official docs: [docs.docker.com/desktop/setup/install/windows-install](https://docs.docker.com/desktop/setup/install/windows-install/)

After install, launch **Docker Desktop** from the Start Menu, accept the license, and let it start (may take a minute). Docker Desktop integrates with WSL2 automatically.

> **Docker subscription note:** Docker Desktop requires a paid subscription for commercial use (enterprises with >250 employees or >$10M annual revenue). Personal use, education, and small teams are free. See [docker.com/pricing](https://www.docker.com/pricing/).

---

## macOS Setup

### Step 1 — Install Xcode Command Line Tools

These provide compilers, Git (older version), and other essentials that many dev tools need.

```bash
xcode-select --install
```

A pop-up will appear — click **Install** and wait for it to finish.

> If the pop-up says "already installed", you're good. To force a re-check: `xcode-select --install` again.

---

### Step 2 — Install Homebrew (Package Manager)

Copy-paste this entire line into **Terminal**:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

You'll be asked for your password and to press **Return** to continue.

> Official docs: [brew.sh](https://brew.sh/) | [docs.brew.sh/Installation](https://docs.brew.sh/Installation)

After install, you may see a **Next steps** message. Run those two lines to add Homebrew to your PATH (they look like this — run exactly what the terminal tells you):

```bash
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

> On Intel Macs the path is `/usr/local/bin/brew` instead of `/opt/homebrew/bin/brew`. The installer output will tell you the correct path.

Verify:

```bash
brew --version
```

You should see `Homebrew 4.x.x`.

---

### Step 3 — Install Git via Homebrew

```bash
brew install git
```

> Official docs: [git-scm.com](https://git-scm.com/)

This installs a **newer** Git than the one from Xcode CLI tools.

---

### Step 4 — Install Docker via Homebrew

```bash
brew install --cask docker
```

> Official docs: [docs.docker.com/desktop/setup/install/mac-install](https://docs.docker.com/desktop/setup/install/mac-install/)

Open Docker from **Applications** or run:

```bash
open /Applications/Docker.app
```

Wait for the Docker whale icon to appear in the menu bar (it's ready when the icon stops animating).

> **Docker subscription note:** Docker Desktop requires a paid subscription for commercial use (enterprises with >250 employees or >$10M annual revenue). Personal use, education, and small teams are free. See [docker.com/pricing](https://www.docker.com/pricing/).

---

## Linux Setup

### Step 1 — Open Terminal

Press `Ctrl + Alt + T` (Ubuntu) or find "Terminal" in your app launcher.

All commands below run in the terminal.

---

### Step 2 — Update Package Manager & Install Git

**Ubuntu / Debian:**

```bash
sudo apt update && sudo apt install git -y
```

**Fedora / RHEL:**

```bash
sudo dnf install git -y
```

**Arch Linux:**

```bash
sudo pacman -S git --noconfirm
```

---

### Step 3 — Install Docker

> Official docs: [docs.docker.com/engine/install](https://docs.docker.com/engine/install/)

**Ubuntu / Debian:**

```bash
sudo apt install docker.io -y && sudo systemctl enable --now docker
```

Add your user to the `docker` group so you don't need `sudo` every time:

```bash
sudo usermod -aG docker $USER
```

> Log out and back in (or restart) for the group change to take effect. Alternatively, run `newgrp docker` in the same session.

**Fedora / RHEL:**

```bash
sudo dnf install docker -y && sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

**Arch Linux:**

```bash
sudo pacman -S docker --noconfirm && sudo systemctl enable --now docker
sudo usermod -aG docker $USER
```

---

## Common Shell Setup (All Platforms)

This section gives you **Zsh** with **Oh My Zsh** and **zsh-autosuggestions** — a vastly improved terminal experience with themes, plugins, and smart command suggestions (grayed-out completions based on your history).

### Windows — Run inside WSL (Ubuntu)

Open Windows Terminal, select the **Ubuntu** tab (not PowerShell). Then run:

### macOS / Linux — Run directly in Terminal

> Official docs: [github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH](https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH)

### Step 1 — Install Zsh (if not already default)

**Windows (WSL Ubuntu):**

```bash
sudo apt install zsh -y
```

**macOS:** Zsh is the default shell since macOS Catalina — skip this step.

**Linux (Ubuntu/Debian):**

```bash
sudo apt install zsh -y
```

**Linux (Fedora):**

```bash
sudo dnf install zsh -y
```

**Linux (Arch):**

```bash
sudo pacman -S zsh --noconfirm
```

---

### Step 2 — Install Oh My Zsh

> Official docs: [ohmyz.sh](https://ohmyz.sh/) | [github.com/ohmyzsh/ohmyzsh](https://github.com/ohmyzsh/ohmyzsh)

Copy-paste this into your terminal:

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

When prompted `Do you want to change your default shell to zsh?`, type **Y** and press Enter.

Oh My Zsh will activate immediately. You'll see a themed prompt.

---

### Step 3 — Install zsh-autosuggestions Plugin

This plugin shows grayed-out suggestions as you type, based on your command history. Press `→` (right arrow) to accept a suggestion.

> Official docs: [github.com/zsh-users/zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)

Clone the plugin into Oh My Zsh's plugin folder:

```bash
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

---

### Step 4 — Enable the Plugin

Open your Zsh configuration file:

```bash
nano ~/.zshrc
```

Find the line that says:

```bash
plugins=(git)
```

Replace it with:

```bash
plugins=(git zsh-autosuggestions)
```

Save: `Ctrl + X` → `Y` → `Enter`.

Apply the change:

```bash
source ~/.zshrc
```

Now as you type commands, you'll see faded suggestions. Hit `→` to autocomplete.

---

## Verification

Run these commands to confirm everything is working.

### Test Git

```bash
git --version
```

Expected output: `git version 2.x.x`

### Test Docker

```bash
docker --version
```

Expected output: `Docker version 2x.x.x`

### Test that Zsh + Oh My Zsh is active

```bash
echo $SHELL
```

Expected output: `/usr/bin/zsh` or `/bin/zsh`

You should also see a themed prompt (not just a plain `$`).

---
