## Profie examples as reference  

*wired connection - static ip*

```bash
nmcli conn add con-name wired-static \  
ifname eth0 \  
ipv4.addresses 192.168.1.10/24 \  
ipv4.dns 8.8.8.8,8.8.4.4 \  
ipv4.method manual \  
ipv4.connection.autostart 1
```  

*wifi connection - dhcp ip*  

```bash
nmcli conn add con-name wifi-dhcp \
type wifi \
ifname wlan 0 \  
ssid MY_WIFI \
wifi-sec.key-mgmt wpa-psk \
wifi-sec.psk superpassword123 \
ipv4.method auto \
```  

```bash
nmcli conn 
```

## Wifi

*Check if wifi is enabled*

```bash
nmcli radio
```

*Enable/disable wifi*

```bash
nmcli radio wifi on|off
```  

*List devices, the interface that fits will have TYPE wifi'

```bash
nmcli dev
```

**note: this will print reachable wifi networks along with some other info such as freq channel, SSID ,security...**
*List reachable wifi networks*

```bash
nmcli dev wifi list ifname <interface> --rescan <yes|no|auto> 
```  

*Connect to a wifi network*  

```bash  
nmcli dev wifi connect <SSID> password <password> ifname <interface> name <connection_name> private <yes|no> hidden <yes|no>
```

*Add a wifi connection profile*  

```
nmcli conn add con-name test type wifi ssid MI_WIFI ipv4.addresses 192.168.1.5/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8,8.8.4.4 ipv4.method manual wifi-sec.key-mgmt wpa-psk wifi-sec.psk <password>
```  

**note: if the command returns smth like '802-11-wireless-security.psk: property is invalid' it means that password is incorrect**
**note2: if you want dhcp, avoid the ipv4.* options in the command and simply add ipv4.method auto**  

## Bridge interface for virtual machines  

*Create a bridge connection profile which creates our virtual interface (e.g vms0)*

```bash
nmcli conn add  
```
