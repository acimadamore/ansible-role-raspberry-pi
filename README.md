Ansible Role - Raspberry Pi
===========================

An Ansible Role for setup a Raspberry Pi with Raspbian OS.

Basic configuration of hostname, locales, timezone, keyboard, network and WiFi. Package installation and system upgrade.

[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-raspberry--pi-blue)](https://galaxy.ansible.com/acimadamore/raspberry_pi/)

Requirements
------------

This role uses Ansible's *json\_query* filter that requires JMESPath to be installed on the control node.

Role Variables
--------------

Variables are listed below along with default values:

Variable                          | Default value | Description/Comment
----------------------------------| --------------| -----------
**raspberry_pi_locale**           | en\_GB.UTF-8  | Locale. See `/usr/share/i18n/SUPPORTED` for full supported list
**raspberry_pi_timezone**         | Europe/London | Time zone. Complete list [here](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones#List)
**raspberry_pi_keyboard_layout**  | gb            | Keyboard layout
**raspberry_pi_hostname**         | raspberrypi   | Hostname of your Pi
**raspberry_pi_configure_network**| false         | Wheter or not to configure the network manually
**raspberry_pi_ip_address**       |               | IP address of the Pi. Use CIDR notation. Required when *raspberry_pi_configure_network* is true
**raspberry_pi_routers**          |               | Default Gateaway of the network. Required when *raspberry_pi_configure_network* is true 
**raspberry_pi_dns_servers**      |               | List of DNS servers to use. Required when *raspberry_pi_configure_network* is true
**raspberry_pi_use_wifi**         | false         | Use Wi-Fi instead of wired connection 
**raspberry_pi_wifi_country**     |               | ISO/IEC alpha2 country code. Complete list [here](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements). Required if you're using a RPi 3 B+ or Higher.
**raspberry_pi_wifi_ssid**        |               | Wi-Fi SSID. Required when *raspberry_pi_use_wifi* is true
**raspberry_pi_wifi_password**    |               | Wi-Fi password. Do not set or leave as an empty string if the Wi-Fi network doesn't have a passphrase
**raspberry_pi_upgrade_system**   | true          | Whether or not to upgrade the system.
**raspberry_pi_packages**         | _see below_   | List of packages to install

The default values are the same used by a fresh install of Raspbian OS. Keyboard model is assumed to be "pc105" with "guess" backspace (check [keyboard(5) man pages](https://manpages.debian.org/jessie/keyboard-configuration/keyboard.5.en.html) for more information).

**Default packages**: apt-transport-https, build-essential, curl, dnsutils, git, htop, lm-sensors, lshw, rsync, screen, unzip, vim.

Dependencies
------------

None.

Example Playbook
----------------

With a Raspberry Pi with SSH enabled and default _pi_ user you could use this sample playbook:

```yaml
---
- hosts: my_rpi
  become: true
  remote_user: pi

  roles:
    - acimadamore.raspberry_pi
```

This playbook will only upgrade the system and install the default packages.

A full example:

```yaml
---
- hosts: my_raspberry_pi
  become: true
  remote_user: pi

  vars:
    raspberry_pi_locale: es_AR.UTF-8
    raspberry_pi_timezone: America/Argentina/Buenos_Aires
    raspberry_pi_keyboard_layout: es
    raspberry_pi_hostname:  my-home-pie
    raspberry_pi_configure_network: yes
    raspberry_pi_ip_address: 192.168.0.2/24
    raspberry_pi_routers: 192.168.0.1
    raspberry_pi_dns_servers: [1.1.1.1, 1.0.0.1]
    raspberry_pi_use_wifi: true
    raspberry_pi_wifi_country: AR
    raspberry_pi_wifi_ssid: myHomeWiFiNetwork
    raspberry_pi_wifi_password: someWifiPassword
    raspberry_pi_packages:
      - git
      - vim

  roles:
    - acimadamore.raspberry_pi
```

License
-------

MIT

Author Information
------------------

[Andrés Cimadamore](https://github/acimadamore)

