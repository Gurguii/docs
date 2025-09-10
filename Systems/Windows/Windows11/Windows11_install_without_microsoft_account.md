
# Install Windows 11 without Microsoft Account 
Start installing Windows with the ISO normally, once the system reaches the **"Choose a country or region"** part, press **shift+f10** to open a cmd prompt, type:
```bash
oobe\BypassNRO.cmd
```  
The system will restart and get back to the installation on the **"Choose a country or region"** part, press **shift+f10** to open a cmd prompt, type:  
```bash
ipconfig /release
```  
Now follow the installation until reaching the Network part, click on **"I don't have internet"** and that's it, you will be prompted for the local account name.  

Once into the Desktop, press **win+r** type **cmd** then type:
```bash
ipconfig /renew
```  
Restarting the host will bring all interfaces back up too.
## Retrieve serial number  
#### wmic
```bash
wmic bios get serialnumber
```
#### powershell
```bash
Get-CimInstance win32_bios | Select-Object -Property SerialNumber
```

## Retrieve model name
#### wmic
```bash
wmic csproduct get identifyingnumber
```
#### powershell
```bash
Get-CimInstance Win32_ComputerSystemProduct | Select-Object -Property Name
```  

## [ POWERSHELL ] Retrieve serial + model  
```bash
Get-CimInstance Win32_ComputerSystemProduct | Select-Object -Property Name, IdentifyingNumber
``` 
*Note: **wmic** isn't available by default in **Windows11***
