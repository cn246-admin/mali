# MALI - ModSecurity Apache Log Inspector

Live inspection of ModSecurity/Apache Log files.

Helps administrators identify ModSecurity triggers for debugging/whitelisting.

Written using [Python 3.9's Standard Library](https://docs.python.org/3.9/library/)
so there should be no need for installing external packages.

-----

## Setup
This was coded and tested on a Debian 11 install of [apache2](https://packages.debian.org/bullseye/apache2)
and [libapache2-mod-security2](https://packages.debian.org/bullseye/libapache2-mod-security2).

Other operating systems may or may not behave differently!

-----

### Configure Apache
- Install the included Apache2 log format configuration file to `/etc/apache2/conf-avaliable/local-log-format.conf`
  * See note below
- Enable the config:
```
sudo a2enconf local-log-format
```

- In `/etc/apache2/sites-available/site.conf`, configure your vhost to use it:
```
LogLevel info ssl:warn
ErrorLog ${APACHE_LOG_DIR}/error.log
CustomLog ${APACHE_LOG_DIR}/access.log extended
```

- Reload apache2:
```
sudo systemctl reload apache2
```

### Install MALI
- As the logfiles are owned by `root:adm`, it's best to add the user who will be using
  MALI to group `adm`:
```
sudo usermod -aG adm $USER
```

- Run from this directory. eg: `./mali`
- OR
- Symlink somewhere in `$PATH`
  - Single user example:
    - `ln -s mali $HOME/.local/bin/mali`
    - `ln -s mali $HOME/bin/mali`
  - Multi user example:
    - `sudo ln -s mali /usr/local/bin/mali`
- Set the location of Apache's access.log and error.log if they differ from the 
  defaults (`/var/log/{apache,error}.log`).

-----

## Use
Not much to using it (yet?).

Just run it in a terminal and it will update the information as requests are made.

If the request has a matching `error.log` entry, it will print the relevant ModSecurity
rule information for further investigation by the Admin.


-----

#### Additional Information
\* This is in regards to the naming of `/etc/apache2/conf-available/local-logs.conf`:
According to `/usr/share/doc/apache2/README.Debian.gz`:
>The local administrator should use file names starting with 'local-' to avoid
>name clashes with files installed by packages.

----

### Links
- Much of this is based on the configs/information/scripts I found on netnea while
  researching ModSecurity. https://www.netnea.com/cms/apache-tutorials/
- https://httpd.apache.org/docs/2.4/mod/mod_log_config.html#formats
- https://httpd.apache.org/docs/2.4/mod/core.html#errorlogformat
