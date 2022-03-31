Establecer nombre del servidor y reiniciar por si acaso

```bash
hostnamectl set-hostname mail.safapi.local
```

Configurar archivo netplan

```netplan
network:
  ethernets:
    enp0s3:
      dhcp4: false
      addresses: [10.0.2.2/24]
      gateway4: 10.0.2.1
      nameservers:
              addresses: [8.8.8.8,8.8.4.4]
    enp0s8:
      dhcp4: true 
  version: 2
```

Configurar /etc/host apuntando a la ip del servidor

* * *

## Configuración bind

/etc/bind/named.conf.local

```
zone "safapi.local" {
        type master;
        file "/etc/bind/db.safapi";
};
```

/etc/bind/db.safapi

```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     ns1.zn.my. root.zn.my. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
        NS      ns1.zn.my.
ns1     IN      A       10.0.2.9
@       IN      MX      10      mail.zn.my.
mail    IN      A       10.0.2.9
host1   IN      A       10.0.2.10
```

Comprobar funcionamiento de zona

```bash
named-checkzone safapi.local /etc/bind/db.safapi
```

Comprobar funcionamiento mx

```bash
dig @10.0.2.2 safapi.local MX +short
```

* * *
## postfix
```
nano /etc/postfix/virtual_mailbox_domains
```

  ```
    $ sudo postmap /etc/postfix/virtual_mailbox_domains 
```
    
4.  Editar  **/etc/postfix/master.cf** 
    
    ```
    $ sudo nano /etc/postfix/master.cf 
    ```
    
5.  cambiar
    
    ```
    ...
    #submission inet n       -       y       -       -       smtpd
    ... 
    ```
    
    eliminar
    
    ```
    ...
    submission inet n       -       y       -       -       smtpd
    ... 
    ```
    


## DOVECOT


    
 ```
    $ sudo apt install -y dovecot-core dovecot-imapd dovecot-pop3d dovecot-lmtpd
```
    

    
 ```
    $ sudo nano /etc/dovecot/conf.d/10-master.conf 
```
    

    
 ```
    ...
    inet_listener imaps {
      port = 993
      ssl = yes
    }
    ... 
```
    
    
    
 ```
    ...
    inet_listener pop3s {
      port = 995
      ssl = yes
    }
    ... 
```
    
 ```
    ...
    service lmtp {
      unix_listener /var/spool/postfix/private/dovecot-lmtp {
        mode = 0600
        user = postfix
        group = postfix
      }
    }
    ... 
```
    
    
```
    ...
    #Postfix smtp-auth
    unix_listener /var/spool/postfix/private/auth {
      mode = 0666
      user = postfix
      group = postfix
    }
    ... 
```
    
    
```
    $ sudo nano /etc/dovecot/conf.d/10-auth.conf 
```
    
```
    disable_plaintext_auth = yes 
```
    
    
```
    auth_mechanisms = plain login 
```

  ```
    $ sudo nano /etc/dovecot/conf.d/10-ssl.conf 
```
    
    
```
    ssl = required 
```
    
```
    #ssl_cert = </etc/dovecot/dovecot.pem
    #ssl_key = </etc/dovecot/private/dovecot.pem 
```
    
