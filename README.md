# selinux-postgres_exporter
SELinux policy for postgres_exporter (https://github.com/prometheus-community/postgres_exporter)

# How to install
The policy consists of three files:

- postgres_exporter.fc # File Contexts, modify this file to match your install location
- postgres_exporter.te # Type Enforcement file
- postgres_exporter.if # Interface file, not used

Make sure `selinux-policy-devel` is installed:
```
dnf install selinux-policy-devel
```

Compile the policy by running:
```
make -f /usr/share/selinux/devel/Makefile postgres_exporter.pp
```

Install the Policy:
```
semodule -i postgres_exporter.pp
```

You might need to relabel the files at the install location.
```
restorecon -FvR /var/opt/prometheus/postgres_exporter
```

# Result
```
[root@server]# ls -lZ /var/opt/prometheus/postgres_exporter/
total 16148
-rw-r--r--. 1 root root system_u:object_r:postgres_exporter_etc_t:s0     11357 Jun 27 11:18 LICENSE
-rw-r--r--. 1 root root system_u:object_r:postgres_exporter_etc_t:s0        70 Jun 27 11:18 NOTICE
-rwxr-xr-x. 1 root root system_u:object_r:postgres_exporter_exec_t:s0 16511957 Jun 27 11:16 postgres_exporter
-rwx------. 1 root root system_u:object_r:postgres_exporter_etc_t:s0       101 Oct  2 11:58 postgres_exporter.env
```

```
[root@server]# pstree -Z | grep -e ^systemd -e postgres
systemd(`system_u:system_r:init_t:s0)
 |-postgres_export(`system_u:system_r:postgres_exporter_t:s0)
 |  `-5*[{postgres_export}(`system_u:system_r:postgres_exporter_t:s0)]
```
