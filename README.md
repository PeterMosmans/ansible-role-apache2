Ansible Role: Apache2
=========

This role installs and configures the Apache 2 webserver on Debian Jessie servers. The main focus is on **hardening a default Apache installation**.
It modifies the default Apache configuration as well as disables and enables specific modules.
Furthermore it can deploy (a number of) website configuration files, SSL certificates and corresponding private keys.
By setting the ```apache2_php``` flag to true, PHP5 will also be installed and configured. Note that it will not be removed or disabled by setting the ```apache2_php``` flag to false. This can be done for instance by adding the php module to the ```apache2_modules_disabled``` list.

By default, the default website configuration will be disabled, and content of the default site (/var/www/html) will be **removed**.

Requirements
------------

The installation of ufw (the uncomplicated firewall, a frontend for iptables).

Role Variables
--------------

Available variables are listed below, along with default values


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



**apache2_php**: When true, PHP5 will also be installed, including the Apache PHP module
```
apache2_php: false
```



**apache2_php_ini**: A list with php.ini settings which will be applied by default. The defaults can be found in ```defaults/main.yml```.
```
apache2_php_ini:
  - name: "open_basedir"
    value: "/var/www/"
  - name: "expose_php"
    value: "Off"
  - name: "allow_url_fopen"
    value: "Off"
  - name: "default_charset"
    value: "\"UTF-8\""
  - name: "enable_dl"
    value: "Off"
  - name: "display_startup_errors"
    value: "display_startup_errors = off"
  - name: "display_errors"
    value: "display_errors = Off"
  - name: "log_errors"
    value: "log_errors = On"
  - name: "assert.active"
    value: "assert.active = 0"
  - name: "mail.add_x_header"
    value: "mail.add_x_header = Off"
  - name: "disable_functions"
    value: "disable_functions = pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriority"
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



**apache2_websites**: An optional list of website configuration files. The ```apache2_website```.conf files are assumed to be in the folder defined by ```protected_storage```.
```
apache2_websites: []
```



**protected_storage**: A location accessible by the Ansible server where the optional configuration files ```apache2_website```.conf, SSL certificates ```ssl_certificates```.cer and the corresponding private keys ```ssl_certificates```.key are stored. It is strongly advised to use a path outside the Ansible 'root' to a secure storage space, and not store important files like private keys in the default location.
```
protected_storage:  ../files
```



**ssl_certificates**: An optional list of certificates and private keys which will be deployed. The ```ssl_certificates```.key as well as ```ssl_certificates```.cer are assumed to be in the folder defined by ```protected_storage```.
```
ssl_certificates: []
```



**www_folder**: The default root where website directories are stored.
```
www_folder: /var/www
```




Please note that this role doesn't template Apache configurations, it copies them. It expects the configurations in the folder```protected_storage``` under the name ```apache2_website```.conf



Dependencies
------------

ufw has to be installed.



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
      - mywebsite
  vars:
    apache2_php: true
      
```
This example will install and harden Apache, install and harden PHP5, deploy the file ```mywebsite.conf``` from the folder ```protected_storage``` and enable the website.




License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans.
