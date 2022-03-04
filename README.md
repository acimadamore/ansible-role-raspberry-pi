# Ansible Role - Raspberry Pi


[![Ansible Galaxy](https://img.shields.io/badge/ansible--galaxy-raspberry--pi-blue)](https://galaxy.ansible.com/acimadamore/raspberry_pi/)
[![CI](https://github.com/acimadamore/ansible-role-raspberry-pi/actions/workflows/test.yml/badge.svg?event=push)](https://github.com/acimadamore/ansible-role-raspberry-pi/actions?query=workflow%3Atest)

An Ansible Role for setup a Raspberry Pi with Raspbian OS.

Basic configuration of hostname, locales, timezone, keyboard, network and WiFi. Package installation. Configuration of /boot/config.txt file.

## Requirements


This role uses Ansible's *json\_query* filter that requires JMESPath to be installed on the control node.

## Role Variables


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
**raspberry_pi_config_options**   | [ ]           | List of options to configure on /boot/config.txt file (see more details below)

The default values are the same used by a fresh install of Raspbian OS. Keyboard model is assumed to be "pc105" with "guess" backspace (check [keyboard(5) man pages](https://manpages.debian.org/jessie/keyboard-configuration/keyboard.5.en.html) for more information).

**Default packages**: apt-transport-https, build-essential, curl, dnsutils, git, htop, lm-sensors, lshw, rsync, screen, unzip, vim.

### Edit /boot/config.txt options

As of version 1.2 it's possible to configure your Raspberry Pi's `/boot/config.txt` file using the `raspberry_pi_config_options` variable:

```yaml
raspberry_pi_config_options:
  - option: arm_freq
    value: 900
  - option: hdmi_mode
    value: 1
  - option: dtoverlay
    regexp: dtoverlay=gpio-ir,gpio_pin=17
    value: gpio-ir,gpio_pin=18
```

For each element specify the corresponding `option` and `value`. The option will be uncommented if necessary and replaced with the new value. If the option is not found in the file, it will be added at the end of it. A third attribute `regexp` is available when an option can be declared multiple times and the search and replace process becomes ambiguous (e.g. "dtoverlay"). In the example above, regexp is used to match the exact line to replace. Any string or regular expression can be used, keep in mind that it will be prefixed with the regex "\^[#\s]*".

Note that options' names and values are **NOT** validated. Check out the [official documentation](https://www.raspberrypi.com/documentation/computers/config_txt.html) for a correct configuration of your Pi.

## Dependencies


None.

## Example Playbook


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
    raspberry_pi_config_options:
      - option: dtoverlay
        value: gpio-ir,gpio_pin=10
      - option: dtoverlay
        regexp: dtoverlay=vc4-kms-v3d
        value: vc5-kms-v4d
      - option: otg_mode
        value: 2

  roles:
    - acimadamore.raspberry_pi
```

## License

MIT

## Author Information

[Andrés Cimadamore](https://github/acimadamore)

