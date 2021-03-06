# pdnsmgrd

## Quickstart

    apt-get install python ssl-cert
    sudo cat /etc/ssl/private/ssl-cert-snakeoil.key > ssl-cert-snakeoil.key
    cp /etc/ssl/certs/ssl-cert-snakeoil.pem ssl-cert-snakeoil.pem
    cp pdnsmgrd.conf.example pdnsmgrd.conf
    editor pdnsmgrd.conf
    su - pdnsmgr
    ./pdnsmgrd


## Features

 * SSL
 * Static Authentication (HTTP Basic Auth)
 * Install/Upgrade pdns packages
 * Start/Stop/Restart pdns daemons
 * Write configuration files
 * Relay to pdns webservers


## Supported URLs

### /do/

Do something.

#### Parameters

 * **action**: _start_, _stop_, _restart_, _install_, _upgrade_, _configure_
 * **target**: _manager_, _recursor_, _auth_
 * For action=configure: **config** - the config file contents
 * For action=install and action=upgrade: **version** - the version to install/upgrade (_optional_)
 * **callback**: _optional_, turns response into JSONP instead of JSON
 
POST with `application/x-www-form-urlencoded` preferred. Result will be JSON or JSONP (see **callback** parameter).

#### Example

    curl -u 'secret:sikrit' -k 'https://localhost:8080/do/?action=restart&target=auth'
    {"cmdline": ["sudo", "service", "pdns", "restart"], "success": true, "output": "Restarting pdns (via systemctl): pdns.service.\n"}


### /server/$servername/$subpath

Relay `$subpath` to configured `$servername`.

GET or POST. Result format will be determined by upstream server.

#### Example

    curl -u 'secret:sikrit' -k https://localhost:8080/server/1/
    {"aaaa-additional-processing": "off"
    , "additional-processing": "off"
    , "allow-from": "127.0.0.0/8, 10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12, ::1/128, fe80::/10"
    ...
    }

## sudo configuration Example

    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns restart
    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns-recursor restart
    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns stop
    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns-recursor stop
    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns start
    pdnsmgr ALL = NOPASSWD: /usr/sbin/service pdns-recursor start
