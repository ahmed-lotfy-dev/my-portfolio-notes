---
title: after install archlinux
date: 2026-01-25
tags:
  - linux-guides
  - after-install-archlinux
share: true
---
## If Gnome install borwser connector 

```plain text
sudo pacman -Sy gnome-browser-connector
```

[Projects/GnomeShellIntegration/Installation – GNOME Wiki Archive](https://wiki.gnome.org/Projects/GnomeShellIntegration/Installation)

## Create ssh keys folder and move keys

---

```plain text
mkdir ~/.ssh

sudo cp /mnt/hdd/Work/keys/github-key ~/.ssh
sudo cp /mnt/hdd/Work/keys/github-key.pub ~/.ssh

sudo chmod 600 ~/.ssh/github-key
sudo chmod 644 ~/.ssh/github-key.pub


```

---

## Install ocs-url for gnome-looks

```plain text
sudo yay ocs-url
```

---

## Install zsh & oh-my-zsh

```plain text
sudo pacman -S zsh
```

```plain text
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

- Install Powerlevel10k using the following command
    - git clone [https://github.com/romkatv/powerlevel10k.git](https://github.com/romkatv/powerlevel10k.git) $ZSH_CUSTOM/themes/powerlevel10k
    - ZSH_THEME="powerlevel10k/powerlevel10k"
    - **p10k configure**
- zsh-syntax-highlighting - It enables highlighting of commands whilst they are typed at a zsh prompt into an interactive terminal. This helps in reviewing commands before running them, particularly in catching syntax errors.
```plain text
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```
- zsh-autosuggestions - It suggests commands as you type based on history and completions.
```plain text
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
```

---

## Fix Ntfs Drive not Mounting

Mount point with disks drive to /mnt/hdd

```plain text
sudo pacman -S ntfs-3g
```

---

## Change gnome looks

- tweaks app to app minimize and expand icons in window top bar

---

## Install Fonts

```plain text
mkdir /home/alotfy/.fonts && sudo cp /mnt/hdd/home-folder/fonts/* /home/alotfy/.fonts
```

---

## Go to gnome extensions then install needed one 

- blur my shell
[https://extensions.gnome.org/extension/3193/blur-my-shell/](https://extensions.gnome.org/extension/3193/blur-my-shell/)
- dash to dock 
[https://extensions.gnome.org/extension/307/dash-to-dock/](https://extensions.gnome.org/extension/307/dash-to-dock/)
- user themes
[https://extensions.gnome.org/extension/19/user-themes/](https://extensions.gnome.org/extension/19/user-themes/)
- clip indicator 
[https://extensions.gnome.org/extension/779/clipboard-indicator/](https://extensions.gnome.org/extension/779/clipboard-indicator/)
- vitals
[https://extensions.gnome.org/extension/1460/vitals/](https://extensions.gnome.org/extension/1460/vitals/)
- extensions list
[https://extensions.gnome.org/extension/3088/extension-list/](https://extensions.gnome.org/extension/3088/extension-list/)


---

## Close multitasking hot corner

---

## Install Git

```plain text
sudo pacman -S git
```

---

## Install Docker

```plain text
sudo pacman -S docker docker-compose docker-buildx

sudo systemctl enable --now docker.service

sudo systemctl is-active docker.service

sudo docker run hello-world
```

### Enabling Non-root Users to Run Docker Commands

```plain text
sudo usermod -aG docker ${USER}

newgrp docker
```

[https://linuxiac.com/how-to-install-docker-on-arch-linux/](https://linuxiac.com/how-to-install-docker-on-arch-linux/)

---

## Install yay (AUR) Package Manager

[https://github.com/Jguer/yay](https://github.com/Jguer/yay)

```plain text
sudo pacman -S --needed git base-devel && git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si
```

---

## Install Node Version Manager

```plain text
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

nvm ls
```

then nvm use lts/something

---

## Fix android emulator GPU error

### ✅ 3. **Enable Hardware Acceleration in AVD Manager**

Open **AVD Manager** in Android Studio:

- Edit your emulator → "Show Advanced Settings"
- Set:
    - **Graphics** to `Hardware - GLES 2.0` or `Hardware - Vulkan`
    - Save & relaunch

[[fix wifi power saving mode|fix wifi power saving mode]]