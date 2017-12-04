systemd_helper
=================

Allow to define systemd:
* [journald.conf](https://www.freedesktop.org/software/systemd/man/journald.conf.html);
* [systemd-tmpfiles](https://www.freedesktop.org/software/systemd/man/tmpfiles.d.html);
* set timezone via [systemd-timedatectl](https://www.freedesktop.org/software/systemd/man/timedatectl.html);
* set console settings via [vconsole.conf](https://www.freedesktop.org/software/systemd/man/vconsole.conf.html)

Requirements
---------------

* Ansible 2.2;
* jmespath;

Extra
------

* journald options is not validate and pass as-is. For all possible options
consult `man journald.conf(5)`;
* tmpfiles are validated by real time execution (online apply settings);
* udev rules syntax validated via external script;

Example configuration
-------------------------

```yaml
---
# systemd-tmpfiles uses the configuration files from the above directories to
# describe the creation, cleaning and removal of volatile and temporary files
# and directories which usually reside in directories such as '/run' or '/tmp'.
# Volatile and temporary files and directories are those located in '/run'
# (and its alias '/var/run'), '/tmp', '/var/tmp', the API file systems such
# as '/sys' or '/proc', as well as some other directories below '/var'.
systemd_tmpfiles:
# This option allow to delete '/etc/tmpfiles.d/*.conf' files before deploy.
- drop_exists: 'true'
  create:
  - file_name: 'set_sda_scheduler'
    path: '/sys/block/sda/queue/scheduler'
    type: 'w'
    arg: 'noop'
  - file_name: 'example1'
    path: '/tmp/example1'
    type: 'd'
    mode: '0755'
    uid: 'root'
    gid: 'root'
    age: '10d'
  clean:
  - file_name: 'example2'
    path: '/tmp/example2'
    type: 'd'
    mode: '0755'
    uid: 'root'
    gid: 'root'
    age: '1m'
  remove:
  - file_name: 'example3'
    path: '/tmp/example3'
    type: 'r'

systemd_tzdata: 'Asia/Yekaterinburg'

systemd_journald_settings:
  Storage: 'persistent'
  SystemMaxUse: '10G'

systemd_udev:
- file_name: '10-persistent-net'
  rules:
  - 'ACTION=="add", SUBSYSTEM=="net", DRIVERS=="?*", ATTR{type}=="32", ATTR{address}=="?*00:02:c9:03:00:31:78:f2", NAME="mlx4_ib3"'
  - 'SUBSYSTEM=="net", ATTR{address}=="18:66:da:e6:be:88", NAME="gig0"'
- file_name: '90-otcash'
  rules:
  - 'ATTRS{idVendor}=="079b", ATTRS{idProduct}=="0028", GROUP="100", MODE="0660", SYMLINK+="ingenico"'
  - 'ATTRS{idVendor}=="abcd", ATTRS{idProduct}=="1980", GROUP="100", MODE="0660", SYMLINK+="rr02"'
  - 'ATTRS{idVendor}=="067b", ATTRS{idProduct}=="2303", GROUP="100", MODE="0660", SYMLINK+="m200"'
  - 'ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", GROUP="100", MODE="0660", SYMLINK+="m200"'

# The '/etc/vconsole.conf' file configures the virtual console, i.e. keyboard
# mapping and console font. It is applied at boot by udev using
# '90-vconsole.rules' file. You can safely mask this file if you want to avoid
# this kind of initialization.
systemd_vconsole:
# Configures the key mapping table for the keyboard.
- keymap: 'ru'
# The KEYMAP_TOGGLE= can be used to configure a second toggle keymap and is by
# default unset.
  keymap_toggle: 'dvorak'
# Configures the console font.
  font: 'cyr-sun16'
# Configures the console map.
  font_map: ''
# Configures the unicode font map.
  font_unimap: ''
```
