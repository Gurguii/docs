### Setup KVM in arch linux

#### Install required packages

```bash
sudo pacman -S libvirt virt-install qemu
```

#### Install a new machine

```bash
sudo virt-install --vcpu 2 \
--memory 2048 \
--cdrom ~/.vms/ISOS/archlinux-2023.03.01-x86_64.iso \
--network bridge=vbr0 \
--noautoconsole \
--disk /home/gurgui/.vms/arch/arch.qcow2,type=<file|raw>,bus=<sata|virtio>,device=<disk|cdrom>,size=100G,format=qcow2 \
--os-variant archlinux \ 
--name arch \
--boot hd,cdrom,network,menu=on,uefi \ 
--graphics vnc,port=5901,passwd='superpassword123!' \
--autostart \
```

notes:  
- memory unit is Megabytes so in the example above 2 gigabytes are given to the VM 
- cdrom will only be used as the first bootable option the first time we run the virt-install command  
- the disk size and format can properties can be omitted if the disk has already been created (qemu-img create -f qcow2|raw <diskname> 100G)  
- if the --autostart flag is used, make sure you've enabled the libvirtd service, else the machine won't get started upon boot (systemctl enable libvirtd)  

### Manage machines

#### Graphical interface

```bash
sudo pacman -S virt-manager
```

#### Command line  

*Manually edit machine definition - xml file*

```bash
virsh edit <machine_name>
```  

*List running machines*

```bash
virsh list
```

*List all machines*

```bash
virsh list --all
```  

*Reset/reboot a machine*

```bash
virsh reset|reboot <machine_name>
```  

*Pause a machine*  

```bash
virsh pause <machine_name>
```  

*Forcefully stop a machine*

```bash
virsh destroy <machine_name>
```  

*Change RAM*

```bash
virsh setmem <machine_name> --size <memory_size_MB>
```  

*Change cpu count*

```bash
virsh setvcpus <machine_name> --count <ncpus>
```  

**There are many many many more options, just run 'virsh help' and read, it's pretty intuitive**  

### Graphical access to machines

*Remmina*

```bash
sudo pacman -S remmina; remmina -c spice|vnc://<ip>:<port>
```  

*Virt viewer*

```
sudo pacman -S virt-viewer; virt-viewer
```
