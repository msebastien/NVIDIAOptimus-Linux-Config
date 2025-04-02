# NVIDIA Optimus Linux Configuration
## Using Nvidia Optimus / iGPU enabled
With Nvidia Optimus *(also called Nvidia PRIME on Linux systems)*, the **integrated GPU** *(iGPU)* is enabled. Use **Optimus-Manager** for switching between the iGPU and dGPU with the possibility to enable Nvidia RTD3 runtime power management support to optimize energy consumption when the dGPU is not used, if available. 

3 Optimus modes are available:

- **Nvidia**: This mode switches to the Nvidia GPU. All graphics and compute workloads will be executed on the Nvidia GPU.
- **Hybrid**: This mode switches to the integrated GPU, but leaves the Nvidia GPU available for on-demand offloading. Similar to how Optimus works on Windows.
- **Integrated**: This mode switches to the integrated GPU, and powers the Nvidia GPU off.

To make the **integrated mode** work correctly, a fixed config file is available. But [my pull request](https://github.com/Askannz/optimus-manager/pull/591) with a fix for this issue has been merged recently, so it is not necessary to use it anymore.

## Using the discrete Nvidia GPU / iGPU disabled
Use the ```11-nvidia_discrete.conf``` X.org config file in the ```/etc/X11/xorg.conf.d/``` directory.

## Nvidia Runtime Dynamic Power Management (RTD3)
- Enable PCI power control:
    - Manual
        ```console
        $ echo "auto" > /sys/bus/pci/devices/0000:01:00.0/power/control
        $ cat /sys/bus/pci/devices/0000:01:00.0/power/control # check if the value is correct
        ```
    - Automated
        1. If it does not already exist, create a file named **80-nvidia-pm.rules** in **/lib/udev/rules.d/**

            ```console
            # touch /lib/udev/rules.d/80-nvidia-pm.rules
            ```

        2. Add the following configuration to the file:
            
            ```
            # Remove NVIDIA USB xHCI Host Controller devices, if present
            ACTION=="add", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x0c0330", ATTR{remove}="1"

            # Remove NVIDIA USB Type-C UCSI devices, if present
            ACTION=="add", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x0c8000", ATTR{remove}="1"

            # Remove NVIDIA Audio devices, if present
            ACTION=="add", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x040300", ATTR{remove}="1"

            # Enable runtime PM for NVIDIA VGA/3D controller devices on driver bind
            ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="auto"
            ACTION=="bind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="auto"

            # Disable runtime PM for NVIDIA VGA/3D controller devices on driver unbind
            ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030000", TEST=="power/control", ATTR{power/control}="on"
            ACTION=="unbind", SUBSYSTEM=="pci", ATTR{vendor}=="0x10de", ATTR{class}=="0x030200", TEST=="power/control", ATTR{power/control}="on"
            ```

- Available Nvidia power management options
    - **disabled** *(0x00)*: the NVIDIA driver will only use the GPU's built-in power management so it always is powered on. Actual power usage will vary with the GPU's workload.

    - **coarse** *(0x01)*: This setting is called coarse-grained power control. With this setting, the NVIDIA GPU driver will allow the GPU to go into its lowest power state when no applications are running that use the nvidia driver stack. Whenever an application requiring NVIDIA GPU access is started, the GPU is put into an active state. When the application exits, the GPU is put into a low power state.

    - **fine** *(0x02)*: This setting is called fine-grained power control. With this setting, the NVIDIA GPU driver will allow the GPU to go into its lowest power state when no applications are running that use the nvidia driver stack. Whenever an application requiring NVIDIA GPU access is started, the GPU is put into an active state. When the application exits, the GPU is put into a low power state. Additionally, the NVIDIA driver will actively monitor GPU usage while applications using the GPU are running. When the applications have not used the GPU for a short period, the driver will allow the GPU to be powered down. As soon as the application starts using the GPU, the GPU is reactivated. Furthermore, the NVIDIA GPU driver controls power to the NVIDIA GPU and its video memory separately. While turning off the NVIDIA GPU, the video memory will be kept in a low power self-refresh mode unless some conditions are met *(see the Nvidia documentation in the links section)*

- Enable Nvidia Runtime Dynamic Power Management (Turing+)
    - Using optimus-manager config file *(/etc/optimus-manager/optimus-manager.conf)*

        ```
        [nvidia]
        dynamic_power_management=fine
        ```

    - Using kernel modules config directory *(/etc/modprobe.d)*

        ```console
        # echo 'options nvidia "NVreg_DynamicPowerManagement=0x02"' >> /etc/modprobe.d/nvidia-pm.conf
        ```

- Check power management status:

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
- [Official X.org config file documentation](https://www.x.org/archive/X11R7.6/doc/man/man5/xorg.conf.5.xhtml)
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
