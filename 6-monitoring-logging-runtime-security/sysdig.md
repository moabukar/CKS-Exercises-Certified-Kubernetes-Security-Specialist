#### Documentation

https://github.com/draios/sysdig/wiki/Sysdig-User-Guide

https://sysdig.com/blog/announcing-sysdig-0-1-98/

#### Install sysdig

```sh

apt install sysdig

```
#### Commands

```sh

sysdig
sysdig proc.name=nano
sysdig proc.name=cat or proc.name=nano

```

```sh

sysdig -l
sysdig proc.name=cat and container.name!=host

```

```sh

csysdig

```

#### Sysdig chisels

[Sysdig chisel](https://sysdig.com/blog/announcing-sysdig-0-1-98/)

```sh

sysdig -c lscontainers
sysdig -cl
sysdig -c topprocs_cpu
sysdig -c spectrogram
sysdig -c spy_logs
sysdig -c spy_users
sysdig -c ps

```
