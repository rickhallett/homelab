# The Air-Gap Survival Kit

*Everything you need when Google isn't an option.*

---

## Why Air-Gap?

The modern developer's workflow:

```
Problem → Google → Stack Overflow → Copy-paste → "Fixed" → Learn nothing
```

The air-gap workflow:

```
Problem → man page → Arch Wiki → Think → Understand → Fixed → Retained
```

One of these builds expertise. One builds dependency.

Air-gapping forces you to:
- Actually read documentation
- Parse error messages instead of pasting them
- Develop debugging intuition
- Become someone who *knows things* instead of someone who *knows how to look things up*

The goal isn't masochism. The goal is competence that survives without connectivity.

---

## USB Drive Structure

```
/mnt/usb/
├── isos/
│   ├── archlinux-2026.02.01-x86_64.iso
│   ├── debian-12.x.x-amd64-netinst.iso
│   └── ubuntu-24.04-live-server-amd64.iso
├── docs/
│   ├── arch-wiki/           # Offline Arch Wiki
│   ├── kthw/                 # Kubernetes the Hard Way
│   ├── k3s-docs/             # k3s documentation
│   ├── tlcl.pdf              # The Linux Command Line
│   └── man-pages/            # Extra man pages
├── lfs/
│   ├── LFS-BOOK-12.x.pdf
│   ├── LFS-BOOK-12.x-HTML/
│   ├── sources/              # All 80+ source tarballs
│   ├── patches/              # All patches
│   └── md5sums               # Verification
└── tools/
    ├── kiwix/                # Offline wiki reader
    └── scripts/              # Helper scripts
```

---

## Download Commands

Run these while you still have internet.

### ISOs

```bash
mkdir -p /mnt/usb/isos

# Arch Linux (get latest from archlinux.org)
curl -LO https://geo.mirror.pkgbuild.com/iso/latest/archlinux-x86_64.iso
mv archlinux-*.iso /mnt/usb/isos/

# Debian 12
curl -LO https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.5.0-amd64-netinst.iso
mv debian-*.iso /mnt/usb/isos/

# Ubuntu 24.04 Server
curl -LO https://releases.ubuntu.com/24.04/ubuntu-24.04-live-server-amd64.iso
mv ubuntu-*.iso /mnt/usb/isos/
```

### Arch Wiki (Offline)

**Option A: Text-only (arch-wiki-docs package)**
```bash
# On an Arch system
sudo pacman -S arch-wiki-docs
cp -r /usr/share/doc/arch-wiki /mnt/usb/docs/arch-wiki
```

**Option B: Full wiki with images (Kiwix ZIM)**
```bash
curl -LO https://download.kiwix.org/zim/other/archlinux_en_all_maxi_2024-06.zim
mv *.zim /mnt/usb/docs/

# Kiwix reader
curl -LO https://download.kiwix.org/release/kiwix-desktop/kiwix-desktop_linux-x86_64.appimage
chmod +x kiwix-desktop*.appimage
mv kiwix-desktop*.appimage /mnt/usb/tools/kiwix/
```

### Linux From Scratch

```bash
mkdir -p /mnt/usb/lfs/sources

# Book (PDF and HTML)
curl -LO https://www.linuxfromscratch.org/lfs/downloads/stable/LFS-BOOK-12.1.pdf
curl -LO https://www.linuxfromscratch.org/lfs/downloads/stable/LFS-BOOK-12.1-HTML.tar.xz
mv LFS-BOOK-* /mnt/usb/lfs/

# Package list and checksums
curl -LO https://www.linuxfromscratch.org/lfs/downloads/stable/wget-list-sysv
curl -LO https://www.linuxfromscratch.org/lfs/downloads/stable/md5sums
mv wget-list-sysv md5sums /mnt/usb/lfs/

# All source packages (~500MB)
cd /mnt/usb/lfs/sources
wget --input-file=../wget-list-sysv --continue

# Verify
md5sum -c ../md5sums
```

### Kubernetes the Hard Way

```bash
git clone https://github.com/kelseyhightower/kubernetes-the-hard-way.git \
    /mnt/usb/docs/kthw
```

### k3s Documentation

```bash
wget --mirror --convert-links --adjust-extension --page-requisites \
    --no-parent https://docs.k3s.io/ -P /mnt/usb/docs/k3s-docs
```

### The Linux Command Line

```bash
curl -LO https://sourceforge.net/projects/linuxcommand/files/TLCL/19.01/TLCL-19.01.pdf
mv TLCL-*.pdf /mnt/usb/docs/tlcl.pdf
```

---

## Pre-Install Packages

On any Arch partition that will go air-gapped, install these *before* disconnecting:

```bash
sudo pacman -S \
    base-devel \
    man-db \
    man-pages \
    texinfo \
    vim \
    neovim \
    tmux \
    git \
    arch-wiki-docs \
    wget \
    curl \
    rsync
```

These are your tools. Treat them like oxygen.

---

## The Air-Gap Discipline

### The Protocol

When stuck:

1. **Read the error message.** Actually read it. It usually says what's wrong.
2. **Check man pages.** `man <command>`, `man 5 <config-file>`
3. **Check info pages.** `info <command>` for GNU tools
4. **Search offline Arch Wiki.** Kiwix or `grep -ri "topic" /path/to/wiki`
5. **Check downloaded docs.** LFS book, KTHW, k3s docs

If still stuck after 30+ minutes of genuine effort:

1. Boot into Omarchy (online partition)
2. **Describe the problem in your own words.** No copy-paste. If you can't articulate it, you don't understand it yet.
3. Search for the solution
4. **Understand the solution** before going back
5. Return to air-gapped partition
6. Apply from understanding, not from clipboard

### The Learning Log

Keep a running log. This is interview material and proof of work.

```bash
cat >> ~/learning-log.txt << 'EOF'

## 2026-02-15 14:30

### Problem
GRUB installation failed: "cannot find a GRUB drive for /dev/sda1"

### What I Tried
- man grub-install
- Searched arch-wiki-docs for "GRUB"
- Found: UEFI mode requires --target=x86_64-efi and --efi-directory

### Solution
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB

### Lesson
GRUB needs to know UEFI vs BIOS explicitly. The error hinted at this. I should read error messages more carefully.

EOF
```

---

## Quick Reference

### Essential Commands

```bash
# Arch package management
pacman -S <pkg>           # Install
pacman -Syu               # Update everything
pacman -Ss <term>         # Search
pacman -Q                 # List installed
pacman -Qi <pkg>          # Package info

# File operations
find /path -name "*.txt"  # Find files by name
grep -r "pattern" /path   # Search content
chmod 755 file            # Permissions
chown user:group file     # Ownership

# System
systemctl status <svc>    # Service status
journalctl -u <svc>       # Service logs
journalctl -f             # Follow all logs
ip addr                   # Network config
ss -tulpn                 # Listening ports

# Disk
lsblk                     # Block devices
fdisk -l                  # Partition tables
mount /dev/sda1 /mnt      # Mount
df -h                     # Disk usage
```

### Man Page Sections

| Section | Content |
|---------|---------|
| 1 | User commands |
| 2 | System calls |
| 3 | Library functions |
| 4 | Special files (/dev) |
| 5 | File formats (/etc) |
| 6 | Games |
| 7 | Miscellaneous |
| 8 | Admin commands |

```bash
man passwd      # Section 1: the passwd command
man 5 passwd    # Section 5: /etc/passwd format
man 8 mount     # Section 8: mount (admin)
```

### Offline Search

```bash
# Search Arch Wiki text files
grep -ri "grub" /usr/share/doc/arch-wiki/text/

# With context
grep -ri -B2 -A5 "grub install" /usr/share/doc/arch-wiki/text/

# Search all man pages
apropos <keyword>
apropos partition
apropos network

# Serve LFS book locally
cd /mnt/usb/lfs/LFS-BOOK-12.1-HTML
python -m http.server 8000
# Open http://localhost:8000
```

---

## Verification Checklist

Before disconnecting:

```bash
# ISOs present
ls -la /mnt/usb/isos/*.iso

# LFS sources complete
cd /mnt/usb/lfs/sources
md5sum -c ../md5sums | grep -v OK  # Should be empty

# Arch Wiki accessible
ls /usr/share/doc/arch-wiki/ || ls /mnt/usb/docs/arch-wiki.zim

# Man pages functional
man pacman
man systemd

# KTHW present
ls /mnt/usb/docs/kthw/README.md

# k3s docs present
ls /mnt/usb/docs/k3s-docs/docs.k3s.io/
```

---

## Emergency Protocol

When truly stuck and local resources are exhausted:

1. **Don't break discipline casually.** Struggle first. The struggle is the learning.
2. Boot to online partition
3. Document: exact error, what you tried, what you expected
4. Search for specific solution
5. **Understand it** — don't just copy
6. Write solution in your learning log
7. Boot back to air-gapped partition
8. Apply from understanding

The goal isn't to never use the internet. The goal is to exhaust local resources first, build intuition, and only reach for the crutch when genuinely necessary.

---

*A developer who can only solve problems with Google is a developer who stops being useful when the connection drops.*

*A developer who can reason from first principles, read documentation, and debug methodically is useful anywhere.*

*Be the second one.*
