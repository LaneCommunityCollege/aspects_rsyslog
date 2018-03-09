# aspects_rsyslog

Install and configure rsyslog

## Requirements

Set `hash_behaviour=merge` in your ansible.cfg file.

## Role Variables

### aspects_rsyslog_enabled
Enable or disable the tasks in this role.

Default is `False`.

Set to `True` to run the tasks.

### aspects_rsyslog_packages
A dictionary/hash of packages to install.

Use this pattern:

```yaml
aspects_rsyslog_packages:
  <package key>:
    state: "<present or latest>"
    <ansible_distribution>:
      <ansible_distribution_version or ansible_distribution_major_version>: "<package name>"
```
Set ```state``` to "default" if you wish to list a package but not install it.

Check the [tasks/aptInstallpackages.yml](tasks/aptInstallpackages.yml) or [tasks/yumInstallPackages.yml](tasks/yumInstallPackages.yml) files to find out what values are accepted for the ```ansible_distribution_*``` variables.


For example:

```yaml
aspects_rsyslog_packages:
  rsyslog:
    state: "present"
    Ubuntu:
      1604: "rsyslog"
      1404: "rsyslog"
    Debian:
      9: "rsyslog"
    CentOS:
      7: "rsyslog"
```

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

### aspects_rsyslog_config_general
A dictionary/hash of rsyslog configuration blocks that will be applied to all hosts, regardless of distribution.

Default value is: `{}`.

The value is passed through the Jinja `sort` filter. So you can control the order your rule blocks are written. Be sure to test before deploying to production!

# Changing configuration
To change configuration, just add a block to the appropriate `aspects_rsyslog_config_*` dictionary/hash.

The `0000000000000default` block is provided because some settings cannot simply be added to the end of the `rsyslog.conf` file. For example, you cannot load the `imtcp` module after the modules section. (At least, that is what happened when I was testing.) So you will need to override the `0000000000000default` block in order to load modules.


# Example Playbook

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

# License

MIT
