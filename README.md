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

## List DRM display connectors (eDP, DP, HDMI)

```bash
for p in /sys/class/drm/*/status; do con=${p%/status}; echo -n "${con#*/card?-}: "; cat $p; done
```

## List of display devices (DFP)
The X log will contain a list of what display devices are valid for the GPU.

```console
$ cat /var/log/Xorg.0.log
``` 

## USEFUL LINKS
- [NVIDIA Driver Documentation](https://download.nvidia.com/XFree86/Linux-x86_64/570.133.07/README/)
- [Officiel X.org config file documentation](https://www.x.org/archive/X11R7.6/doc/man/man5/xorg.conf.5.xhtml)
- [X.org config file information (in french)](https://www.malekal.com/configurer-comprendre-xorg-conf/)
- [X.org display modes and resolutions (in french)](https://doc.ubuntu-fr.org/resolution_ecran_avance)
- [Kernel Mode Setting (KMS)](https://wiki.archlinux.org/title/Kernel_mode_setting)
- [NVIDIA Driver on Arch Wiki](https://wiki.archlinux.org/title/NVIDIA)
- [Arch Linux Boot process](https://wiki.archlinux.org/title/Arch_boot_process)
- [Nvidia Optimus on Arch wiki](https://wiki.archlinux.org/title/NVIDIA_Optimus)
- [Optimus Manager on Github](https://github.com/Askannz/optimus-manager)
- [Patched version of GDM for Optimus Manager](https://aur.archlinux.org/pkgbase/gdm-prime)
- [Optimus Manager Qt GUI](https://github.com/Shatur/optimus-manager-qt)
- [Envycontrol, an alternative to Optimus Manager, on Github](https://github.com/bayasdev/envycontrol)
