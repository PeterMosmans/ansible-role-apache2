Ansible Role: Apache2
=====================

Build status for this role: [![Build Status](https://travis-ci.org/PeterMosmans/ansible-role-apache2.svg)](https://travis-ci.org/PeterMosmans/ansible-role-apache2)


This role installs and configures the Apache 2 webserver on Debian and Ubuntu
servers. The main focus is on **hardening a default Apache installation**. It
modifies the default Apache configuration as well as disables and enables
specific modules. Furthermore it can deploy (a number of) website configuration
files, SSL certificates and corresponding private keys.

By setting the
```apache2_php``` flag to true, PHP will also be installed and configured.

Note that PHP will not be removed or disabled by setting the ```apache2_php```
flag to false. This can be done for instance by adding the php module to the
```apache2_modules_disabled``` list.


Requirements
------------

The installation of ufw (the uncomplicated firewall, a frontend for iptables).


Role Variables
--------------

Available variables are listed below, along with default values.

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



**apache2_php**: When true, PHP will also be installed, including the Apache PHP
module
```
apache2_php: false
```


**apache2_php_version**: The PHP version. The default can be found in
```defaults/main.yml```.
```
apache2_php_version: 7.0

```



If PHP will be installed, `php.ini` will be deployed to
`/etc/php/[apache2_php_version]/apache2/php.ini`. This is a template which uses lots of
customizable template variables. The defaults can be found in ```defaults/main.yml```.
```
apache2_php_allow_url_fopen: "Off"
apache2_php_allow_url_include: "Off"
apache2_php_assert_active: "0"
apache2_php_default_charset: "\"UTF-8\""
apache2_php_disable_functions: "fsockopen,pcntl_alarm,pcntl_fork,pcntl_waitpid,pcntl_wait,pcntl_wifexited,pcntl_wifstopped,pcntl_wifsignaled,pcntl_wexitstatus,pcntl_wtermsig,pcntl_wstopsig,pcntl_signal,pcntl_signal_dispatch,pcntl_get_last_error,pcntl_strerror,pcntl_sigprocmask,pcntl_sigwaitinfo,pcntl_sigtimedwait,pcntl_exec,pcntl_getpriority,pcntl_setpriorit,stream_socket_client"
apache2_php_display_errors: "Off"
apache2_php_display_startup_errors: "Off"
apache2_php_enable_dl: "Off"
apache2_php_expose_php: "Off"
apache2_php_log_errors: "On"
apache2_php_mail_add_x_header: "Off"
apache2_php_open_basedir: "/dev/urandom:/var/www"
```


**apache2_ports**: A list on which Apache will listen. If this variable is not defined, port 80 (and 443) will be used. Example:
```
apache2_ports:
  - 80
  - 8000
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



**apache2_websites**: An optional list with Apache configuration files. The `src` points to the Jinja2 file, the `dest` will be the resulting website configuration file.
Example:
```
apache2_websites:
  - src: mywebsited.conf.j2
    name: mywebsite.conf
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




Please note that this role doesn't template Apache configurations - it copies configuration files. It does however template PHP.



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
This example will install and harden Apache, install and harden PHP, deploy the file ```mywebsite.conf``` from the folder ```roles/apache2/files``` and enable the website. The default website will be disabled, and `/var/www/html` removed.




License
-------

GPLv3


Author Information
------------------

Created by Peter Mosmans.
