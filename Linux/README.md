# General Linux Setup For ODBC
This guide will help you install UnixODBC in your linux environment.

### Currently Supported Linux Flavors:
- Alpine: 3.12
- CentOS: 7, 8
- RockyLinux: 8.5
- Ubuntu: 16.04, 18.04, 20.04, 21.10, 22.04

## 1. Install the Proper Driver Manager
Install the most recent version of unixODBC if it is already not installed on your system. This is the ODBC Driver Manager and will help manage different drivers for different DB systems. 

Select the appropriate commands in your terminal for your chosen OS Flavor.
```SHELL
# Alpine 3.12
sudo apk add unixODBC
```
```SHELL
# CentOS 7, CentOS 8
sudo yum -y install unixODBC
```
```SHELL
# CentOS 8, RockyLinux 8.5
sudo dnf -y install unixODBC
```
```SHELL
# Ubuntu 16.04, 18.04, 20.04, 21.10, 22.04
sudo apt-get -y install unixodbc-bin
```