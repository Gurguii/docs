# Setting up Apache as a load balancer  
### 1. Install httpd package
```bash
sudo pacman -S apache
```  
### 2. Enable required modules 

`proxy_module`  
`proxy_balancer_module`  
`proxy_http_module`  

Also, a balancing method module must be enabled:  

| Module Name | Description |
| :--- | :--- |
| `lbmethod_byrequests_module` | Distributes new connections to the worker with the fewest active requests. |
| `lbmethod_bybusyness_module` | Distributes new connections to the least busy worker. |
| `lbmethod_heartbeat_module` | Distributes new connections to the worker with the lowest latency, determined by a heartbeat system. |
| `lbmethod_bytraffic_module` | Distributes new connections to the worker with the least amount of traffic. |

While in other distributions such as Debian / Ubuntu, we have the a2enmod, a2dismod utilities to enable/disable apache modules, in arch linux we must edit the configuration file:  
```bash
nvim /etc/httpd/conf/httpd.conf
```  
Uncomment (remove leading #) from the appropiate LoadModule lines and run:
```bash
sudo systemctl reload httpd
```  
#### Alternative to manually editing  
- Enable proxy_module:  
    `sudo sed -i "s/#LoadModule proxy_module/LoadModule proxy_module/" /etc/httpd/conf/httpd.conf`  
- Enable proxy_balancer_module:  
    `sudo sed -i "s/#LoadModule proxy_balancer_module/LoadModule proxy_balancer_module/" /etc/httpd/conf/httpd.conf`  
- Enable proxy_http_module:  
    `sudo sed -i "s/#LoadModule proxy_http_module/LoadModule proxy_http_module/" /etc/httpd/conf/httpd.conf`  
- Enable load balancing method:  
    `sudo sed -i "s/#LoadModule <lb_mod_name>/LoadModule <lb_mod_name>/" /etc/httpd/conf/httpd.conf`  

### 3. Setup a load balancer 
```
<VirtualHost *:80>
    <Proxy balancer://custom_cluster>
        BalancerMember http://gurgui1.example.com
        BalancerMember http://gurgui2.example.com
	    BalancerMember http://gurgui3.example.com

	    # Possible values: byrequests, bytraffic, bybusyness, byheartbeat
        ProxySet lbmethod=byrequests
    </Proxy>

    # Route incoming traffic to the balancer
    ProxyPass / balancer://custom_cluster/
    ProxyPassReverse / balancer://custom_cluster/
</VirtualHost>
```  
### 4. Load balancer manager  
Balancers' can be monitored and managed through a web GUI:  
```
    <Location /balancer-mgmt>
        SetHandler balancer-manager
	    Require host localhost
    </Location>
```  
Now, when visiting http://<load_balancer_ip>/balancer-mgmt, we'll have access to the workers' status and management.  

**Note:** the `Require host localhost` ensures that the manager can only be accessed from the load balancer server.  
Also, `Require ip <ip/netmask>` can be used.  

⚠️ **IMPORTANT** - in a setup like the one we are using, where all traffic '/' is redirected to the balancers, we **must** avoid the Proxy from working in the balancer manager's URL, since it will only be available in the server and not the workers. `ProxyPass /balancer-mgmt !` 

### 5. Final vhost entry  
```
<VirtualHost *:80>
    <Proxy balancer://custom_cluster>
        BalancerMember http://gurgui1.example.com:9001
        BalancerMember http://gurgui2.example.com:8080
	    BalancerMember http://gurgui3.example.com

	    # Possible values: byrequests, bytraffic, bybusyness, byheartbeat
        ProxySet lbmethod=byrequests
    </Proxy>

    <Location /balancer-mgmt>
        SetHandler balancer-manager
	    Require host localhost
    </Location>

    ProxyPass /balancer-mgmt !

    # Route incoming traffic to the balancer
    ProxyPass / balancer://custom_cluster/
    ProxyPassReverse / balancer://custom_cluster/
</VirtualHost>
```  
### 6. restart apache service  
```bash 
sudo systemctl restart httpd
```
