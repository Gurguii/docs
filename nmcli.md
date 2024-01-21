## Profie examples as reference  

*bridge interface*
**I personally use this to make my vms be in my lan**  

#### Create the bridge profile

```bash
nmcli conn add con-name bridge \
ifname vbr0 \
type bridge \
stp no \
ipv4.addresses 192.168.1.1/24 \
ipv4.dns 8.8.8.8,8.8.4.4 \
ipv4.method manual \
ipv4.connection.autostart 1
```  

#### Create the slave profile

```bash
nmcli conn add con-name slave-host \
ifname eth0 \
type bridge-slave \
master bridge
```  

After adding these 2 profiles, just bring them up with 'nmcli conn up <connection_name>', it will make the physical network iface (eth0)  
a slave of the virtual (vbr0), this way we can just add vms to the bridge and they will have lan/internet access  

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

```bash
nmcli conn add con-name test type wifi ssid MI_WIFI ipv4.addresses 192.168.1.5/24 ipv4.gateway 192.168.1.1 ipv4.dns 8.8.8.8,8.8.4.4 ipv4.method manual wifi-sec.key-mgmt wpa-psk wifi-sec.psk <password>
```  

**note: if the command returns smth like '802-11-wireless-security.psk: property is invalid' it means that password is incorrect**  
**note2: if you want dhcp, avoid the ipv4.* options in the command and simply add ipv4.method auto**  

### Create hotspot  

#### Using 'device' subcommand

```bash
nmcli device wifi hotspot ssid MY_WIFI password superpass123! ifname wlan0
```  

*this way nmcli will add the connection profile for you with the SSID name*  

#### Using 'connection' subcommand

PSK(pre shared key) protocol  

```bash
nmcli connection add con-name "ap-profile" ifname <wifi-iface> ssid <ssid> type wifi wifi.mode ap wifi-sec.key-mgmt wpa-psk wifi-sec.psk <password> 
```

EAP(enterprise access protocol)

```bash
nmcli connection add con-name "enterprise-ap-profile" ifname <wifi-ifae> ssid <ssid> type wifi wifi.mode ap wifi-sec.key-mgmt wpa-eap 802-1x.eap <tls|ttls...> 802-1x.identity <username> 802-1x.phase2-auth mschapv2
```
