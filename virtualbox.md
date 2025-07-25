# VirtualBox on Linux

## Boot order

Move optical drive to the top of the list in the boot order.

## Boot error

For error:

```log
Kernel driver not installed (rc=-1908)

The VirtualBox Linux kernel driver is either not loaded or not set up correctly. Please try setting it up again by executing

'/sbin/vboxconfig'

as root.

If your system has EFI Secure Boot enabled you may also need to sign the kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load them. Please see your Linux system's documentation for more information.

where: suplibOsInit what: 3 VERR_VM_DRIVER_NOT_INSTALLED (-1908) - The support driver is not installed. On linux, open returned ENOENT.
```

1. Run `sudo dnf install kernel-devel kernel-headers`

2. Run `sudo /sbin/vboxconfig` to rebuild the kernel modules for VirtualBox.

## Enter real full-screen mode

1. In the VirtualBox menu bar Devices → Insert Guest Additions CD image…

2. Inside the VM, open File Explorer → go to the mounted CD → run VBoxWindowsAdditions.exe.

3. Follow the install wizard and reboot when done.

4. After reboot, in the VM window: Go to View → Auto-resize Guest Display
