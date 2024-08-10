# Apache setup quickref

This document was made specificly for Arch Linux, but I'm pretty sure it will work similarly with most Linux  
distributions, adapting the package manager, maybe package name...

### Install the apache package
```bash
sudo pacman -Sy && sudo pacman -S apache --noconfirm
```

## Examples

#### Virtualhost
```bash
<VirtualHost *:9999>
    <Directory "/home/gurgui/github/opoweb">
        AllowOverride None
        Require all granted
    </Directory>
    Options Indexes FollowSymLinks
    
    ServerName opoweb.com
    DocumentRoot "/home/gurgui/github/opoweb"         
    
    ErrorLog "/home/gurgui/github/opoweb/apache_error.log"
    CustomLog "/home/gurgui/github/opoweb/apache_access.log" common
    
    LoadModule php_module modules/libphp.so
    AddType "application/x-httpd-php" "php"
    
    SSLEngine on
    SSLCertificateFile "/etc/ssl/certs/opoweb_crt.pem"
    SSLCertificateKeyFile "/etc/ssl/private/opoweb_key.pem"
</VirtualHost>
```
| Keyword | Description | Possible Values |
|---|---|---|
| Options | Sets file system access controls and features for a directory. | Indexes, FollowSymLinks, MultiViews, Includes, ExecCGI, ... |
| ServerName | Specifies the hostname or IP address for a virtual host. | Domain name (e.g., example.com), IP address |
| DocumentRoot | Defines the directory containing the website files served by a virtual host. | Path to a directory (e.g., /var/www/html) |
| CustomLog | Specifies the location and format for access logs. | File path, log format (e.g., combined, common) |
| ErrorLog | Specifies the location for error logs generated by Apache. | File path |
| Require | Controls access to resources based on conditions. | all granted, user user1, ip 192.168.1.0/24, ... |
| AllowOverride | Determines which directives can be overridden in `.htaccess` files. | None, All, Options, FileInfo, AuthConfig, Limit, ... |  

Notes:
- When 'combined' is used in a CustomLog directive, access, agent and referer information will be in the same log file  
- When using the SSL directives, there might be other modules that need to be enabled, plus the VirtualHosts definition should be placed in **/etc/httpd/conf/extra/httpd-ssl.conf**  

## Permissions
Make sure that rather the http user or group has access to the DocumentRoot and files within it. Also just give write permissions whenever the server is supposed to require it.  

# Others

## Enable PHP support
```bash
sudo pacman -S php-apache
```  
This command will install:  
| File | Description |
|---|---|
| usr/lib/httpd/modules/libphp.so | shared library required to have php working
| etc/httpd/conf/extra/php_module.conf | configures the php module

#### Add the following line to apache configuration file, /etc/httpd/conf/httpd.conf
```bash
LoadModule php_module modules/libphp.so
```
This will make apache enable such library upon restart, enabling effectively using PHP code

### Add the following line to /etc/httpd/conf/mime.types  
```bash
application/x-httpd-php php
```  

### Restart the apache service so it loads the PHP module
```bash
sudo systemctl restart httpd
```  

## IMPORTANT
**LoadModule** and **AddType** directives can be added within <VirtualHost> tags to enable certain modules and extensions for such VHOST as shown in the example above.  

This will make apache appropiately associate the .php extensions with the header.