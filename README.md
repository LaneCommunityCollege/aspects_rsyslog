# aspects_rsyslog

Install and configure rsyslog

## Requirements

Set `hash_behaviour=merge` in your ansible.cfg file.

## Role Variables

### aspects_rsyslog_enabled
Enable or disable the tasks in this role.

Default is `False`.

Set to `True` to run the tasks.

### aspects_rsyslog_custom_config_file_path
The absolute path to the custom configuration file this role stores configuration blocks in. This file is included at the end of `/etc/rsyslog.conf` so that it can override any defaults that the various distributions set.

Default value is: `/etc/rsyslog.conf`.

### aspects_rsyslog_config_centos7
A dictionary/hash of rsyslog configuration blocks specific to the CentOS7 distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `centos/7` Vagrant box in February 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_trusty
A dictionary/hash of rsyslog configuration blocks specific to the Ubuntu 14.04 Trusty distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `ubuntu/trusty64` Vagrant box in February 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_xenial
A dictionary/hash of rsyslog configuration blocks specific to the Ubuntu 16.04 Xenial distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `bento/ubuntu-16.04` Vagrant box in February 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_stretch
A dictionary/hash of rsyslog configuration blocks specific to the Debian 9 Stretch distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `debian/stretch64` Vagrant box in February 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_oraclelinux7
A dictionary/hash of rsyslog configuration blocks specific to the Oracle Linux 7 distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `http://yum.oracle.com/boxes/oraclelinux/ol74/ol74.box` Vagrant box in August 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_oraclelinux6
A dictionary/hash of rsyslog configuration blocks specific to the Oracle Linux 6 distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `http://yum.oracle.com/boxes/oraclelinux/ol69/ol69.box` Vagrant box in August 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_bionic
A dictionary/hash of rsyslog configuration blocks specific to the Ubuntu 18.04 bionic distribution.

Default value is in `0000000000000default`. The complete rsyslog.conf copied from the `ubuntu/bionic64` Vagrant box in August 2018.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

### aspects_rsyslog_config_general
A dictionary/hash of rsyslog configuration blocks that will be applied to all hosts, regardless of distribution.

Default value is: `{}`.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

# Changing configuration
To change configuration, just add a block to the appropriate `aspects_rsyslog_config_*` dictionary/hash.

The `0000000000000default` block is provided because some settings cannot simply be added to the end of the `rsyslog.conf` file. For example, you cannot load the `imtcp` module after the modules section. (At least, that is what happened when I was testing.) So you will need to override the `0000000000000default` block in order to load modules.

# Dependencies
## aspects_packages
[aspects_packages](https://github.com/LaneCommunityCollege/aspects_packages) is used to ensure the rsyslog package is installed.

If you don't want to use `aspects_packages`, just set `aspects_packages_enabled: False`.

# Example Playbook
```yaml
- hosts: servers
  vars:
    aspects_rsyslog_enabled: True
    aspects_rsyslog_config_centos7:
      00001kern: |
        #This is a comment
        # Kernel messages are stored in the kernel file,
        # critical messages and higher ones also go
        # to another host and to the console
        kern.* /var/adm/kernel kern.crit @finlandia kern.crit /dev/console kern.info;kern.!err /var/adm/kernel-info
      00002mailinfo: |
        # Log all mail.info and news.info messages to info
        mail,news.=info /var/adm/info
    aspects_rsyslog_config_trusty:
      00001kern: |
        #This is a comment
        # Kernel messages are stored in the kernel file,
        # critical messages and higher ones also go
        # to another host and to the console
        kern.* /var/adm/kernel kern.crit @finlandia kern.crit /dev/console kern.info;kern.!err /var/adm/kernel-info
      00002mailinfo: |
        # Log all mail.info and news.info messages to info
        mail,news.=info /var/adm/info
      00003fileowner: |
        #
        # Set the default permissions for all log files.
        #
        $FileOwner syslog
        $FileGroup adm
        $FileCreateMode 0640
        $DirCreateMode 0755
        $Umask 0022
        $PrivDropToUser syslog
        $PrivDropToGroup syslog
    aspects_rsyslog_config_xenial:
      00001kern: |
        #This is a comment
        # Kernel messages are stored in the kernel file,
        # critical messages and higher ones also go
        # to another host and to the console
        kern.* /var/adm/kernel kern.crit @finlandia kern.crit /dev/console kern.info;kern.!err /var/adm/kernel-info
      00002mailinfo: |
        # Log all mail.info and news.info messages to info
        mail,news.=info /var/adm/info
    aspects_rsyslog_config_stretch:
      00001kern: |
        #This is a comment
        # Kernel messages are stored in the kernel file,
        # critical messages and higher ones also go
        # to another host and to the console
        kern.* /var/adm/kernel kern.crit @finlandia kern.crit /dev/console kern.info;kern.!err /var/adm/kernel-info
      00002mailinfo: |
        # Log all mail.info and news.info messages to info
        mail,news.=info /var/adm/info
    aspects_rsyslog_config_general:
      00001comment: |
        # This comment will apply to all hosts.
  roles:
     - aspects_rsyslog
```
# License

MIT
