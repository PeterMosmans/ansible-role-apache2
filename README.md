Role Name
=========

This role installs Apache2 on Debian Jessie servers.
It deploys [apache2_website].conf files and SSL certificates (if specified), hardens the default Apache configuration, disables and enables specific modules.
By default, the default website configuration will be disabled, and content of the default site (/var/www/html) will be **removed**.

Requirements
------------

The installation of ufw (the Uncomplicated firewall, a frontend for iptables).

Role Variables
--------------

Available variables are listed below, along with default values
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
A list with modules which will be disabled by default. The defaults can be found in ```defaults/main.yml``.

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
A list with modules which will be enabled by default. The defaults can be found in ```defaults/main.yml``.


```
protected_storage:  ../files
```
A folder accessible by the Ansible server where the configuration files ```apache2_website```.conf, the certificates ```ssl_certificates```.cer and the private keys ```ssl_certificates```.key are stored. It is strongly advised to use a path to a secure storage space, and not store important files lie private keys in the default folder.

```
ssl_certificates: []
```
An optional list of certificates and private keys which will be deployed. The ```ssl_certificates```.key as well as ```ssl_certificates```.cer are assumed to be in the folder defined by ```protected_storage```.

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
A list with security.conf settings which will be enabled by default. The defaults can be found in ```defaults/main.yml``.


```
apache2_websites: []
```
An optional list of website configuration files. The ```apache2_website```.conf files are assumed to be in the folder defined by ```protected_storage```.

```
www_folder: /var/www
```
The default root where website directories are stored.


Please note that this role doesn't template Apache configurations. It expects the configurations in the folder```protected_storage``` under the name ```apache2_website```.conf


Dependencies
------------

None - althoug ufw has to be installed.

Example Playbook
----------------

- hosts: all
  become: yes
  become_method: sudo  
  roles:
    - role: PeterMosmans.apache2
      apache2_websites:
        - kanboard

This example will deploy the file ```kanboard.conf``` from the folder```protected_storage``` and enable the website.

License
-------

GPLv3

Author Information
------------------

Created by Peter Mosmans.
