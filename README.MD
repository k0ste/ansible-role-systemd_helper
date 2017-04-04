ansible-systemd_helper
=======================

Allow to define systemd configs (actually only journald) and deploy
[systemd-tmpfiles](https://www.freedesktop.org/software/systemd/man/tmpfiles.d.html).

Ansible versions
-----------------

Ansible 2.2 and jmespath is required.

Extra
------

journald options is not validate and pass as-is. For all possible options
read `man journald.conf`.

Example configuration
-------------------------

```yaml
---
systemd_journald_settings:
  Storage: 'persistent'
  SystemMaxUse: '10G'

systemd_tmpfiles:
- create:
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
```
