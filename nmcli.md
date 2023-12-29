## Wifi
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
nmcli conn add con-name test type wifi ssid <ssid> ip4 <ip>/<masksize> gw4 <ipv4_gateway> wifi-sec.key-mgmt wpa-psk wifi-sec.psk <password>
```  
**note: if the command returns smth like '802-11-wireless-security.psk: property is invalid' it means that password is incorrect**
