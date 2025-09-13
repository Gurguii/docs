# Using samba share as volume in docker

Basicly, we have to add a volume with the following driver options:
```bash
volumes:
  shared:
    driver_opts:
      type: cifs
      o: username=${SMB_USERNAME},password=${SMB_PASSWORD},uid=0,gid=0,vers=3.0
      device: //${SMB_HOST}/${SMB_SHARE}
```  
Where:  
- `type: cifs` indicates that we want to use a samba share (cifs protocol)  
- `o:  username=${SMB_USERNAME},password=${SMB_PASSWORD},uid=0,gid=0,vers=3.0` are the different options that we pass, these options will then be passed to `mount.cifs`, so any valid option for such command are suitable here  
- `device: //${SMB_HOST}/${SMB_SHARE}` host/share that we want to mount from  

**Note:** If doing a ***swarm*** deploy, `.env` variables won't be shared among all nodes, in such case, secrets must be used to store sensitive variables.

⚠️ **IMPORTANT -**  the `uid` and `gid` must be the same as the user running the inside the container, else permission will be denied.

### Fully working yaml file for a `swarm`
```yaml
services:
  webserver:
    image: httpd:latest
    ports:
      - "9001:80"
    volumes:
      - "shared:/usr/local/apache2/htdocs"
    networks:
      - custom_nw
    deploy:
      replicas: 2
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 120s
    secrets:
      - smb_username
      - smb_password
      - smb_host
      - smb_share

volumes:
  shared:
    driver_opts:
      type: cifs
      o: uid=0,gid=0,vers=3.0,username=$$(cat /run/secrets/smb_username),password=$$(cat /run/secrets/smb_password)
      device: //$$(cat /run/secrets/smb_host)/$$(cat /run/secrets/smb_share})

networks:
  custom_nw:
    driver: overlay

secrets:
  smb_username:
    external: true
  smb_password:
    external: true
  smb_host:
    external: true
  smb_share:
    external: true
```  
