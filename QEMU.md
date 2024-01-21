# QEMU

# Check CPU

```console
$ egrep -c '(vmx|svm)' /proc/cpuinfo
```

# Installation

```console
$ sudo apt install -y qemu-system virt-manager virtinst libvirt-clients bridge-utils libvirt-daemon-system
```


- **qemu-system** – This is an open-source emulator that emulates the hardware resources of a computer.
- **virt-manager** – A Qt-based GUI interface for creating and managing virtual machines using the libvirt daemon.
- **virtinst** – A collection of command-line utilities for creating and making changes to virtual machines.
- **libvirt-clients** – APIs and client-side libraries for managing virtual machines from the command line.
- **bridge-utils** – A set of command-line tools for managing bridge devices.
- **libvirt-daemon-system** – Provides configuration files needed to run the virtualization service.


# Enabling and Starting `libvirtd`

```console
$ sudo systemctl enable --now libvirtd
$ sudo systemctl start libvirtd
```

# Adding Users to `kvm` and `libvirt`

```console
$ sudo usermod -aG kvm $USER
$ sudo usermod -aG libvirt $USER
```

After this we need a reboot, or a logout->login

# Launching `virt-manager`

```console
$ sudo virt-manager
```
