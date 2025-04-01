# NVIDIA Optimus Linux Configuration
## HYBRID/OPTIMUS MODE
Use Optimus-Manager (in "nvidia" or "hybrid" mode) with Nvidia RTD3 runtime power management support.
To make work the "integrated" mode (for disabling the Nvidia discrete GPU), a fixed config file is available.

## DISCRETE MODE / Disconnected iGPU
Use the "11-nvidia_discrete.conf" X.org config file in this folder.

## Check Nvidia RTD3 Power Management status

```console
$ cat /proc/driver/nvidia/gpus/0000:01:00.0/power
```

## Check Nvidia driver params

```console
$ cat /proc/driver/nvidia/params
```

## List DRM display connectors

```bash
for p in /sys/class/drm/*/status; do con=${p%/status}; echo -n "${con#*/card?-}: "; cat $p; done
```

## USEFUL LINKS
- [NVIDIA Driver Documentation](https://download.nvidia.com/XFree86/Linux-x86_64/570.133.07/README
- [X.org config file information (in french)](https://www.malekal.com/configurer-comprendre-xorg-conf/)
- [Kernel Mode Setting (KMS)](https://wiki.archlinux.org/title/Kernel_mode_setting)
- [NVIDIA Driver on Arch Wiki](https://wiki.archlinux.org/title/NVIDIA)
- [Arch Linux Boot process](https://wiki.archlinux.org/title/Arch_boot_process)
- [Nvidia Optimus on Arch wiki](https://wiki.archlinux.org/title/NVIDIA_Optimus)
- [Optimus Manager on Github](https://github.com/Askannz/optimus-manager)
- [Patched version of GDM for Optimus Manager](https://aur.archlinux.org/pkgbase/gdm-prime)
- [Optimus Manager Qt GUI](https://github.com/Shatur/optimus-manager-qt)
- [Envycontrol, an alternative to Optimus Manager, on Github](https://github.com/bayasdev/envycontrol)
