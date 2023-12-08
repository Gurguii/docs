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
--disk /home/gurgui/.vms/arch/arch.qcow2,bus=virtio,device=disk \
--os-variant archlinux \ 
--name arch \
--boot hd,cdrom,network,menu=on \ 
--graphics vnc,port=5901 \
--boot uefi
```

