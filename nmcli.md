# * Profile - bridge * 

#### Bridge profile

```bash
nmcli conn add con-name MyBridge \
ifname vbr0 \
type bridge \
stp no \
connection.autoconnect yes \
connection.autoconnect-slaves yes \
ipv4.method manual \
ipv4.addresses 192.168.1.24/24 \
ipv4.gateway 192.168.1.1 \
ipv4.dns 8.8.8.8,8.8.4.4 \
```  
*note: ifname refers to the name of the virtual interface that will be created*
#### Slave profile

```bash
nmcli conn add con-name slave-host \
ifname eth0 \
type ethernet \
master vbr0 \

``` 
*note1: the ifname  must be the physical iface with internet access*  
*note2: you can add as many virtual ifaces as you want, but at least a physical iface with internet access is required*

# * Profile - wired ethernet & static ip*

```bash
nmcli conn add con-name wired-static \  
ifname eth0 \  
ipv4.addresses 192.168.1.10/24 \  
ipv4.dns 8.8.8.8,8.8.4.4 \  
ipv4.method manual \  
ipv4.connection.autostart 1
```  

# * Profile - WPA/PSK wifi - dhcp *

```bash
nmcli conn add con-name wifi-dhcp \
type wifi \
ifname wlan0 \  
ssid TARGET_WIFI \
wifi-sec.key-mgmt wpa-psk \
wifi-sec.psk superpassword123 \
ipv4.method auto
```  

# * Profile - WPA/EAP wifi - static * 
Useful e.g in environments using radius for centralized network client authentication  
```bash
nmcli conn add con-name enterprise-wifi-static \
type wifi \
ifname wlan0 \
ssid TARGET_WIFI \
wifi-sec.key-mgmt wpa-eap \
802-1x.eap ttls \
802-1x.identity "radius_user_1" \
802-1x.password "radius_pass_1" \
802-1x.phase2-auth mschapv2 \
```
*note: i haven't fully tested this command*  
*note2: if you don't want to write the password in plain text, omit the 802-1x.password option and use --ask when bringing the connection up*  

# * Profile - dot1q VLAN tagging *  
```
nmcli conn add con-name vlan10 \
type vlan \
dev enp6s0 \
id 10
```  

# Wifi stuff

*Check if wifi is enabled*

```bash
nmcli radio
```

*Enable/disable wifi*

```bash
nmcli radio wifi on|off
```  

*List devices, the interface that fits will have TYPE 'wifi'*

```bash
nmcli dev
```

*List reachable wifi networks*

```bash
nmcli dev wifi list ifname <interface> --rescan <yes|no|auto> 
```  
*Connect to WPA/PSK wifi network*  

```bash  
nmcli dev wifi \
connect WIFI_SSID \
password superpass123! \
ifname wlan0 \
name HomeWifi \
private <yes|no> \
hidden <yes|no> \
band <a|bg>
```  
*note: if the command returns  '802-11-wireless-security.psk: property is invalid' the password is incorrect*  


### Create hotspot  

Let nmcli create the connection profile
```bash
nmcli dev wifi hotspot \
ssid MY_WIFI \
password superpass123! \
ifname wlan0
```  

Create it ourselves
```bash
nmcli conn add \
con-name test \
type wifi \
ssid MI_WIFI \
ipv4.addresses 192.168.1.5/24 \
ipv4.gateway 192.168.1.1 \
ipv4.dns 8.8.8.8,8.8.4.4 \
ipv4.method manual \
wifi-sec.key-mgmt wpa-psk \
wifi-sec.psk superpass123!
```
