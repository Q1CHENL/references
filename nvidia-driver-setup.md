# Full Reset & Reinstallation of NVIDIA Driver on Fedora

## 0. Boot into Command Line

Press `Shift` (or `Esc` on some systems) during boot to enter the GRUB menu.

From the GRUB menu:

1. Press `e` on the Fedora boot entry.

2. Find the `linux` or `linuxefi` line.

3. At the end, add:

   ```bash
   systemd.unit=multi-user.target
   ```

4. Press `Ctrl + X` or `F10` to boot into **text-only mode**.

---

## 1. Remove Existing NVIDIA/Nouveau Drivers

```bash
sudo dnf remove '*nvidia*' 'xorg-x11-drv-nouveau*'
sudo dnf autoremove
```

---

## 2. Install Required Build Tools

```bash
sudo dnf groupinstall "Development Tools"
sudo dnf install dkms kernel-devel kernel-headers gcc elfutils-libelf-devel
```

---

## 3. Blacklist the `nouveau` Driver

Create a config file:

```bash
sudo nano /etc/modprobe.d/blacklist-nouveau.conf
```

Add:

```conf
blacklist nouveau
options nouveau modeset=0
```

Save and exit (`Ctrl + O`, Enter, `Ctrl + X`).

---

## 4. Rebuild Initramfs

```bash
sudo dracut --force
```

---

## 5. Install RPM Fusion and NVIDIA Drivers

### 5.1 Enable RPM Fusion (if not already)

```bash
sudo dnf install \
  https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

### 5.2 Install the NVIDIA Driver (Recommended: akmod)

**akmod vs kmod:**

- `akmod-nvidia`: Automatically rebuilds the driver module when the kernel updates (recommended)
- `kmod-nvidia`: Pre-compiled driver for the current kernel only (faster install, but may break on kernel updates)

```bash
sudo dnf install akmod-nvidia
```

Optional for CUDA support:

```bash
sudo dnf install xorg-x11-drv-nvidia-cuda
```

---

## 6. Force Xorg to Use the NVIDIA Driver

Create:

```bash
sudo mkdir -p /etc/X11/xorg.conf.d
sudo nano /etc/X11/xorg.conf.d/10-nvidia.conf
```

Paste:

```conf
Section "Device"
    Identifier "Nvidia Card"
    Driver "nvidia"
    VendorName "NVIDIA Corporation"
EndSection
```

---

## 7. Disable Wayland (Optional but Recommended for NVIDIA)

Edit GDM config:

```bash
sudo nano /etc/gdm/custom.conf
```

Uncomment or add:

```ini
WaylandEnable=false
```

---

## 8. Restore Graphical Boot

```bash
sudo systemctl set-default graphical.target
```

---

## 9. Reboot

```bash
sudo reboot
```

---

## 10. Post-Reboot Verification

Once booted:

- Check if NVIDIA driver is working:

  ```bash
  nvidia-smi
  ```

- Check loaded modules:

  ```bash
  lsmod | grep nvidia
  ```

- Check that Xorg is using the NVIDIA driver:

  ```bash
  grep -i nvidia /var/log/Xorg.0.log
  ```

---

## Troubleshooting

If you're stuck again:

- Reboot and add `systemd.unit=multi-user.target` at GRUB again
- Check the output of:

  ```bash
  uname -r
  rpm -q kernel-devel
  ```

If they **don’t match**, fix with:

```bash
sudo dnf install "kernel-devel-uname-r == $(uname -r)"
```
