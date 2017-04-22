NIS
-----

To configure a NIS server and client in RHEL7.

-----

### For NIS server, it is possible to check whether the following packages are installed on the system using the command rpm -q <package-name>, e.g.

    rpm -q ypserv ypbind rpcbind yp-tools

### If any of these packages are not installed they can be installed by the command yum install <package-name>

    yum install ypserv ypbind rpcbind yp-tools -y

### Perform the following steps to configure the NIS server
#### 1.The NIS domain needs to be added in /etc/sysconfig/network Edit the file and add the following line to the end of the file

    NISDOMAIN=DOMAIN-NAME

#### Also can execute the command below so that the domain name is set immediately without rebooting system

    ypdomainname DOMAIN-NAME

#### Verify that it is set correctly

    ypdomainname


