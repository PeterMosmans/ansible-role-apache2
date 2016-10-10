Ansible Role: Apache2
=====================

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-apache2.svg)](https://travis-ci.org/PeterMosmans/ansible-role-apache2)


This role installs and configures the Apache 2 webserver on Debian and Ubunty servers. The main focus is on **hardening a default Apache installation**.
It modifies the default Apache configuration as well as disables and enables specific modules.
Furthermore it can deploy (a number of) website configuration files, SSL certificates and corresponding private keys.
By setting the ```apache2_php``` flag to true, PHP5 will also be installed and configured.
Note that it will not be removed or disabled by setting the ```apache2_php``` flag to false. This can be done for instance by adding the php module to the ```apache2_modules_disabled``` list.

By default, the default website configuration will be disabled, and content of the default site (/var/www/html) will be **removed**.

Requirements
------------

The installation of ufw (the uncomplicated firewall, a frontend for iptables).

Role Variables
--------------

Available variables are listed below, along with default values

**apache2_default**: When true, the default site will *not* be disabled, and `/var/www/html` will *not* be removed. If not specified or false, the default site will be disabled, and `/var/www/html` removed.
```
apache2_default: false
```
By default, the value is not specified.


**apache2_modules_disabled**: A list with Apache modules which will be disabled by default. The defaults can be found in ```defaults/main.yml```.
```
apache2_modules_disabled:
  - autoindex
  - authn_anon
  - cgi
  - dav
  - env
  - negotiation
  - setenvif
  - status
  - userdir
```




**apache2_modules_enabled**: A list with Apache modules which will be enabled by default. The defaults can be found in ```defaults/main.yml```.
```
apache2_modules_enabled:
  - alias
  - auth_digest
  - authz_host
  - deflate
  - dir
  - headers
  - reqtimeout
  - rewrite
  - ssl
```



**apache2_open_basedir**: The open_basedir variable in the php.ini file. Optional, if not specified this will default to `/var/www`. See also **apache2_php_ini**.



**apache2_php**: When true, PHP5 will also be installed, including the Apache PHP module
```
apache2_php: false
```



**apache2_php_ini**: A list with php.ini settings which will be applied by default. The defaults can be found in ```defaults/main.yml```.
```
apache2_php_ini:
  - name: "open_basedir"
    value: "{{ apache2_open_basedir|default('/var/www/') }}"
  - name: "expose_php"
    value: "Off"
  - name: "allow_url_fopen"
    value: "Off"
  - name: "default_charset"
    value: "\"UTF-8\""
  - name: "enable_dl"
    value: "Off"
  - name: "display_startup_errors"
    value: "off"
  - name: "display_errors"
    value: "Off"
  - name: "log_errors"
    value: "On"
  - name: "assert.active"
    value: "0"
  - name: "mail.add_x_header"
    value: "Off"
  - name: "disable_functions"
    value: "fsockopen,pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriorit,stream_socket_client"
```



**apache2_security_conf**: A list with security.conf settings which will be applied by default. The defaults can be found in ```defaults/main.yml```.
```
apache2_security_conf:
  - name: "Header set X-Content-Type-Options:"
    value: "\"nosniff\""
  - name: "Header set X-Frame-Options:"
    value: "\"sameorigin\""
  - name: "ServerName"
    value: "{{ ansible_fqdn }}"
  - name: "ServerTokens"
    value: "Prod"
  - name: "ServerSignature"
    value: "Off"
  - name: "TraceEnable"
    value: "Off"
```



**apache2_websites**: An optional list containing the location (```src```) and name (```name```) of website configuration files. Please note that the location is relative from the apache2 ```role/files``` subfolder. For instance, if you want to include a file from within another role ```myrole/files```, you should use the following:
```
apache2_websites:
  - src: ../../myrole/files
    name: www.mysite.com.conf


```
By default, the list is empty.




**ssl_certificates**: An optional list containing the location (```src```) and name (```name```) of x.509 SSL certificates. Please note that the location is relative from the apache2 ```role/files``` subfolder. For instance, if you want to include a certificate from within a secure storage path, you should use the following:
```
ssl_certificates:
  - src: /secure/storage/path
    name: www.mysite.com.cer


```
By default, the list is empty.




**ssl_keys**: An optional list containing the location (```src```) and name (```name```) of private keys. Please note that the location is relative from the apache2 ```role/files``` subfolder. For instance, if you want to include a key from within a secure storage path, you should use the following:
```
ssl_certificates:
  - src: /secure/storage/path
    name: www.mysite.com.key
```
By default, the list is empty.




**www_folder**: The default root under which website directories are stored.
```
www_folder: /var/www
```




Please note that this role doesn't template Apache configurations - it copies configuration files.



Dependencies
------------

None.



Example Playbook
----------------
```
- hosts: all
  become: yes
  become_method: sudo
  roles:
    - role: PeterMosmans.apache2
```
This example will install and harden Apache.


```
- hosts: all
  become: yes
  become_method: sudo
  roles:
    - role: PeterMosmans.apache2
      apache2_websites:
      - src: .
        name: mywebsite.conf
  vars:
    apache2_php: true
      
```
This example will install and harden Apache, install and harden PHP5, deploy the file ```mywebsite.conf``` from the folder ```roles/apache2/files``` and enable the website. The default website will be disabled, and `/var/www/html` removed.




License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans.
