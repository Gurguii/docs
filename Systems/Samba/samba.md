## [Arch Linux]

### Install samba

```bash
 sudo pacman -S --noconfirm samba
```

### Configuration file

#### Location

```bash
/etc/samba/smb.conf
```

#### Example

```bash
[global]

server role = standalone 
netbios name = arch1to
workgroup = WORKGROUP
security = user
map to guest = bad user
unix charset = UTF-8
wins support = yes

local master = yes
preferred master = yes
os level = 65

[share]
comment = Gurgui shared folder
path = /home/gurgui/shared
browseable = yes
read only = yes
guest ok = no
valid users = gurgui
```

### Tools

#### smbpasswd

Used to manage users, e.g:

```bash
smbpasswd -a <user>
```

This would prompt for a password, if both match, the user will be added

***Note: <user> must exist locally***

#### pdbedit

Access samba database, e.g:

```bash
pdbedit -L
```

This would output a list of samba users
