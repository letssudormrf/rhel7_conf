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

#### 2.Use server HOSTNAME for the local domain, the NIS Server also needs to be set up as a client. Edit the /etc/yp.conf file and add the following line

    ypserver 127.0.0.1

#### 3.Use systemctl to enable and restart the daemons NIS needs

    systemctl enable ypserv.service yppasswdd.service ypxfrd.service
    systemctl restart ypserv.service yppasswdd.service ypxfrd.service

#### 4.Next, edit /var/yp/Makefile to reflect the following (if it needs that it can do not merge the passwd file with the shadow file)

    MERGE_PASSWD=false

#### If you don't want some of the map built, ensure to build the shadow file(add the 'shadow' to the end of line)

    all:  passwd group hosts rpc services netid protocols mail shadow

#### 5.Generate the NIS database (for 32 bit the version older than RHEL7):

    /usr/lib/yp/ypinit -m

#### For 64-bit installations(This command requires the NIS server name to be provided)

    /usr/lib64/yp/ypinit -m

#### If the NIS database is updated(Such as user add), it can use the following command for updating 

    make -C /var/yp/

#### 6.Join into the NIS server

    authconfig --update --nisdomain=your-domain --nisserver=your-server --enablenis

#### also you can check whether the services are ok not not by the following command.

    yptest
    ypwhich
    ypcat passwd

#### 7.For security settings, create /etc/securenets file to configure ACL for permitting the specified clients to connect the server.(for details to read man ypserv)


    vim /etc/securenets

    A sample securenets file might look like this:

    # allow connections from local host -- necessary
    host 127.0.0.1
    # same as 255.255.255.255 127.0.0.1
    #
    # allow connections from any host
    # on the 131.234.223.0 network
    255.255.255.0   131.234.223.0
    # allow connections from any host
    # between 131.234.214.0 and 131.234.215.255
    255.255.254.0   131.234.214.0

#### Also for security settings it is necessary to permit the specified port connections, specify ypserv yppasswdd ypxfrd ports and firewall-cmd rules should be added(check which port use "rpcinfo -p")

    vim /etc/sysconfig/network
    YPSERV_ARGS="-p 834"
    YPXFRD_ARGS="-p 835"

    vim /etc/sysconfig/yppasswdd
    YPPASSWDD_ARGS="--port 836"

    systemctl restart rpcbind.service ypserv.service ypxfrd.service yppasswdd.service
    firewall-cmd --permanent --add-service=rpc-bind --zone=public
    firewall-cmd --permanent --add-port=834-836/tcp --zone=public
    firewall-cmd --permanent --add-port=834-836/udp --zone=public
    firewall-cmd --reload

### Perform the following steps to configure the NIS client
#### 1.Install the necessary NIS client packages and join into server

    yum install ypbind yp-tools -y
    authconfig --update --nisdomain=your-domain --nisserver=your-server --enablenis --enablemkhomedir
    systemctl restart ypbind.service

#### It is easier to join into NIS by the authconfig-gtk command(it can select the "Create home directories on the first login")

    yum install authconfig-gtk -y
    authconfig-gtk

#### 2.Check the user information from NIS Server

    yptest
    ypcat passwd
