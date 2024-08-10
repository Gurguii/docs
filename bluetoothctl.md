#### Bluetoothctl  

Utility to manage bluetooth  installed with bluez-utils package  

### Scan for nearby devices  

```bash
bluetoothctl scan <on|off>
```  

*this will start/stop scanning, and devices along with their mac addresses will be printed to console*  

### List devices  

```bash
bluetoothctl devices
```  

### Pair to a device

```bash
bluetoothctl pair <device_mac_address>
```  

### Connect to a device  

```bash
bluetoothctl connect <device_mac_addres>
```
