# Custom Windows entry in GRUB
I've had incovenients a few times when setting up a dualboot system with Arch Linux being installed in a different physical disk than Windows, so that's why I came up with this.

## Choose your location:
| File                   | Persistence after ***updates***         | Immediate Application  |
|----------------------------|---------------------------------------|------------------------|
| **/boot/grub/grub.cfg**  | No | Instant                |
| **/etc/grub.d/40_custom**| Yes                                   | Requires ***update*** |

***updates*** refers to `update-grub` or `grub-mkconfig -o /boot/grub/grub.cfg`
## Add the entry  
Replace:  
`<ENTRYNAME>` with the name you want to appear in the Grub menu upon boot  
`<UUID>` with the UUID of the boot partition of the desired operating system.  
`<LOCATION>` with one of the paths mentioned before.  

***Note: elevated privileges are required to run this command***
```bash
cat << EOF >> <LOCATION>
menuentry "<ENTRYNAME>" --class windows --class os $menuentry_id_option 'osprober-efi-<UUID>' {
    insmod ntfs
    insmod fat
    insmod part_gpt
    insmod part_msdos
    search --no-floppy --fs-uuid --set=root <UUID> 
    chainloader /EFI/Microsoft/Boot/bootmgfw.efi
}
EOF
