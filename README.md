# MALI - ModSecurity Apache Log Inspector

Live inspection of ModSecurity/Apache Log files.

Helps administrators identify ModSecurity triggers for debugging/whitelisting.

Written using [Python 3.9's Standard Library](https://docs.python.org/3.9/library/)
so there should be no need for installing external packages.

<br>

## Setup
This was coded and tested on a Debian 11 install of [apache2](https://packages.debian.org/bullseye/apache2)
and [libapache2-mod-security2](https://packages.debian.org/bullseye/libapache2-mod-security2).

Other operating systems may or may not behave differently!

------

### Configure Apache
-    Install the included Apache2 log format configuration file to `/etc/apache2/conf-avaliable/local-log-format.conf` (See note below)
-    Enable the config:
     ```bash
     sudo a2enconf local-log-format
     ```

-   In `/etc/apache2/sites-available/site.conf`, configure your vhost to use it:
     ```apache
     LogLevel info ssl:warn
     ErrorLog ${APACHE_LOG_DIR}/error.log
     CustomLog ${APACHE_LOG_DIR}/access.log extended
     ```

-    Reload apache2:
     ```bash
     sudo systemctl reload apache2
     ```

<br>

### Install MALI
-    As the logfiles are owned by `root:adm`, it's best to add the user who will be using
     MALI to group `adm`:
     ```bash
     sudo usermod -aG adm $USER
     ```

-    Run from this directory. eg: `./mali`
-    OR
-    Symlink somewhere in `$PATH`
  -    Single user example: `ln -s mali $HOME/.local/bin/mali` or `ln -s mali $HOME/bin/mali`
  -    Multi user example: `sudo ln -s mali /usr/local/bin/mali`
-    Set the location of Apache's access.log and error.log if they differ from the
     defaults (`/var/log/{apache,error}.log`).

<br>

## Use MALI
Run it in a terminal with either the `-a` flag for access.log or the `-e` flag
for error.log and it will update the information as requests are made.

If the access.log has a matching unique id in the error.log, the unique_id will
be colored red in the access log output.

This works great if you use a [screen](https://www.gnu.org/software/screen/) or [tmux](https://github.com/tmux/tmux) session
with the screen split down the middle. One side is running `mali -a` and the other
side running `mali -e`.

<br>

## Output
**Access log:**
Run with `mali -a`:
```
Request Time: 20:39:09.850956-0500
Unique ID: YeIlvUbeVrdtlNQf-J34AgAASAc
Remote Host: 108.162.219.153
User Agent: Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
HTTP Referer: https://www.chucknemeth.com/laptop/lenovo-x230/flash-lenovo-x230-coreboot
HTTP Request: GET /_media/laptop/lenovo-x230/coreboot/x230-nconfig-02-mainboard.png?w=250&h=134&tok=b0b111 HTTP/2.0
HTTP Status: 200
SSL Protocol: TLSv1.3
SSL Cipher: TLS_AES_256_GCM_SHA384

Request Time: 20:39:09.864622-0500
Unique ID: YeIlvUbeVrdtlNQf-J34BQAATxY
Remote Host: 108.162.219.21
User Agent: Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
HTTP Referer: https://www.chucknemeth.com/laptop/lenovo-x230/flash-lenovo-x230-coreboot
HTTP Request: GET /_media/usb-device/ch341a/3v-mod/ch341a-mosi-3.3v.jpg?w=250&h=165&tok=d1e2f7 HTTP/2.0
HTTP Status: 200
SSL Protocol: TLSv1.3
SSL Cipher: TLS_AES_256_GCM_SHA384

Request Time: 20:39:09.863513-0500
Unique ID: YeIlvVqOFYgP5NBhUWaSWwAABxQ
Remote Host: 108.162.219.105
User Agent: Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
HTTP Referer: https://www.chucknemeth.com/laptop/lenovo-x230/flash-lenovo-x230-coreboot
HTTP Request: GET /_media/usb-device/ch341a/ch341a-back-pins.jpg?w=600&tok=0579b2 HTTP/2.0
HTTP Status: 200
SSL Protocol: TLSv1.3
SSL Cipher: TLS_AES_256_GCM_SHA384

Request Time: 20:39:11.524603-0500
Unique ID: YeIlv975zdFGMETO5h9maAAAgQk
Remote Host: 108.162.219.9
User Agent: Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
HTTP Referer: https://www.chucknemeth.com/laptop/lenovo-x230/flash-lenovo-x230-coreboot
HTTP Request: GET /linux HTTP/2.0
HTTP Status: 200
SSL Protocol: TLSv1.3
SSL Cipher: TLS_AES_256_GCM_SHA384

Request Time: 20:39:11.678392-0500
Unique ID: YeIlv975zdFGMETO5h9maQAAhgk
Remote Host: 108.162.219.9
User Agent: Mozilla/5.0 (X11; Linux x86_64; rv:96.0) Gecko/20100101 Firefox/96.0
HTTP Referer: https://www.chucknemeth.com/linux
HTTP Request: GET /lib/exe/taskrunner.php?id=linux&1642210751 HTTP/2.0
HTTP Status: 200
SSL Protocol: TLSv1.3
SSL Cipher: TLS_AES_256_GCM_SHA384
```

**Error log:**
Run with `mali -e`:
```
`Time: Fri Jan 14 19:13:08.042305 2022
Unique ID: YeIRlN75zdFGMETO5h9l7AAAlxQ
Remote Host: 108.162.219.37
CRS Rule ID: 942360 (/usr/share/modsecurity-crs/rules/REQUEST-942-APPLICATION-ATTACK-SQLI.conf)
HTTP Referer: https://www.chucknemeth.com/proxmox/kvm/kvm-automated
Client: 108.162.219.37
Msg: Detects concatenated basic SQL injection and SQLLFI attempts
Severity: CRITICAL

Time: Fri Jan 14 19:13:08.042798 2022
Unique ID: YeIRlN75zdFGMETO5h9l7AAAlxQ
Remote Host: 108.162.219.37
CRS Rule ID: 949110 (/usr/share/modsecurity-crs/rules/REQUEST-949-BLOCKING-EVALUATION.conf)
HTTP Referer: https://www.chucknemeth.com/proxmox/kvm/kvm-automated
Client: 108.162.219.37
Msg: Inbound Anomaly Score Exceeded (Total Score: 5)
Severity: CRITICAL

Time: Fri Jan 14 19:13:08.070661 2022
Unique ID: YeIRlN75zdFGMETO5h9l7AAAlxQ
Remote Host: 108.162.219.37
CRS Rule ID: 980130 (/usr/share/modsecurity-crs/rules/RESPONSE-980-CORRELATION.conf)
HTTP Referer: https://www.chucknemeth.com/proxmox/kvm/kvm-automated
Client: 108.162.219.37
Msg: Inbound Anomaly Score Exceeded (Total Inbound Score: 5 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): individual paranoia level scores: 5, 0, 0, 0

Time: Fri Jan 14 19:43:46.406725 2022
Unique ID: YeIYwlqOFYgP5NBhUWaSVAAAABA
Remote Host: 183.136.225.56
CRS Rule ID: 920350 (/usr/share/modsecurity-crs/rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf)
HTTP Referer: -
Client: 183.136.225.56
Msg: Host header is a numeric IP address
Severity: WARNING

Time: Fri Jan 14 19:47:05.394211 2022
Unique ID: YeIZid75zdFGMETO5h9mKwAAAIE
Remote Host: 128.14.134.134
CRS Rule ID: 920350 (/usr/share/modsecurity-crs/rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf)
HTTP Referer: -
Client: 128.14.134.134
Msg: Host header is a numeric IP address
Severity: WARNING
```

-----

#### Additional Information
In regards to the naming of `/etc/apache2/conf-available/local-logs.conf`:  
According to `/usr/share/doc/apache2/README.Debian.gz`:  
>The local administrator should use file names starting with 'local-' to avoid
>name clashes with files installed by packages.

----

### Links
- Much of this is based on the configs/information/scripts I found on netnea while
  researching ModSecurity. https://www.netnea.com/cms/apache-tutorials/
- https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#formats
- https://httpd.apache.org/docs/2.4/mod/core.html#errorlogformat
