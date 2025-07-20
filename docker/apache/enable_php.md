# Enabling PHP on apache container

## Connect to the docker container  
```
docker exec -it <container_name> bash
```
#### 1. Install required dependencies
```
apt install -y php libapache2-mod-php
```  
#### 2. Copy module to apache's module folder
```
cp /usr/lib/apache2/modules/libphp8.2.so /usr/local/apache2/modules
```  
*Note that version might change depending on when you refer to this document*
#### 3. Edit apache configuration file - /usr/local/apache2/conf/httpd.conf
##### 3.1 Add LoadModule line
```
LoadModule php_module modules/libphp8.2.so
```
*Note that version might change depending on when you refer to this document*
##### 3.2 Add FilesMatch directive
```
<FilesMatch \.php$>
  SetHandler application/x-httpd-php
</FilesMatch>
```  
#### 4. Add php mime type line - /usr/local/apache2/conf/mime.types  
```
application/x-httpd-php 		        php
```  
#### 5. Check configuration
```
apachectl configtest
```  
#### 6. Restart Apache  
```
apachectl -k restart
```  
#### 7. Verify php is working  
Create a file under ***/usr/local/apache2/htdocs/index.php*** with the following contents:
```
<?php phpinfo(); ?>
```
## Optional
#### Change default Ifmodule dir_module directive to priorize *index.php* over *index.html*  
```
<Ifmodule dir_module>
  DirectoryIndex index.php index.html
</Ifmodule>
```  

