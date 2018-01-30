Ubuntu Basics
===================

*************************************
How To Create a Sudo User on Ubuntu 
*************************************

.. code-block :: shell

       adduser username

Set and confirm the new user's password at the prompt. A strong password is highly recommended!

.. code-block :: shell

        Set password prompts:
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully

Follow the prompts to set the new user's information. It is fine to accept the defaults to leave all of this information blank.

.. code-block :: shell

        User information prompts:
        Changing the user information for username
        Enter the new value, or press ENTER for the default
            Full Name []:
            Room Number []:
            Work Phone []:
            Home Phone []:
            Other []:
        Is the information correct? [Y/n]

Use the usermod command to add the user to the sudo group.

.. code-block :: shell

        usermod -aG sudo username

By default, on Ubuntu, members of the sudo group have sudo privileges.
Use the su command to switch to the new user account.

.. code-block :: shell

        su - username

.. code-block :: shell

        sudo ls -la /root

********************************
Set static IP Ubuntu 16.04
********************************

.. code-block :: shell

        sudo nano /etc/network/interfaces

and paste this under ``# The primary network interface:``

.. code-block :: shell

        auto enp0s25
        iface enp0s25 inet static
        address 192.168.0.16
        netmask 255.255.255.0
        gateway 192.168.0.1
        dns-nameservers 8.8.4.4 8.8.8.8

.. code-block :: shell

        sudo /etc/init.d/networking restart

********************************************
HA proxy
********************************************
HAProxy(High Availability Proxy) is an open source load balancer which can load balance any TCP service. It is particularly suited for HTTP load balancing.

**Installing HAProxy**

 Use the apt-get command to install HAProxy.
 
 .. code-block:: shell

       Apt-get update
       apt-get install haproxy

 We need to enable HAProxy to be started by the init script.
 ``nano /etc/default/haproxy``
 Set the ENABLED option to 1
 ``ENABLED=1``
 To check if this change is done properly execute the init script of HAProxy without any parameters. You should see the following.

 .. code-block:: shell

       root@haproxy:~# service haproxy
       Usage: /etc/init.d/haproxy {start|stop|reload|restart|status}

**Configuring HAProxy**

 We'll move the default configuration file and create our own one.
 ``mv /etc/haproxy/haproxy.cfg{,.original}``
 Create and edit a new configuration file:
 ``nano /etc/haproxy/haproxy.cfg``

 .. code-block:: shell

        global
            log 127.0.0.1 local0 notice
            maxconn 2000
            user haproxy
            group haproxy
        defaults
            log     global
            mode    http
            option  httplog
            option  dontlognull
            retries 3
            option redispatch
            timeout connect  5000
            timeout client  10000
            timeout server  10000
        listen stats
            bind 192.168.11.92:80
            mode http
            log global
            stats enable
            stats realm Haproxy\ Statistics
            stats uri /haproxy_stats
            stats hide-version
            stats auth admin:card4me@321
            balance roundrobin
            option httpclose
            option forwardfor
            server svr1 192.168.11.100:80
            server svr2 192.168.11.101:80


*******************************************
Installing keepalived
*******************************************

**Configuring HAProxy and Keepalived**

Install HAProxy and Keepalived on both ubuntu nodes.

.. code-block:: shell

       apt-get install haproxy 
       apt-get install keepalived

Load balancing in HAProxy also requires the ability to bind to an IP address that are nonlocal, meaning that it is not assigned to a device on the local system. Below configuration is added so that floating/shared IP can be assigned to one of the load balancers. 

Good, keepalived is now installed. Before we proceed with configuring keepalived itself, edit the following file:

``sudo nano /etc/sysctl.conf``

Add the below lines.

.. code-block:: shell

       net.ipv4.ip_nonlocal_bind=1
      

To enable the changes made in sysctl.conf you will need to run the command.

.. code-block:: shell

       sysctl -p
       Output: net.ipv4.ip_nonlocal_bind = 1

Now let’s create keepalived.conf file on each instances. 

.. code-block:: shell

       sudo nano  /etc/keepalived/keepalived.conf

Add the below configuration on the master node

.. code-block:: shell

        global_defs {
        # Keepalived process identifier
        lvs_id haproxy_DH
        }
        # Script used to check if HAProxy is running

        vrrp_script check_haproxy {
        script "killall -0 haproxy"
        interval 2
        weight 2
        }
        # Virtual interface
        # The priority specifies the order in which the assigned interface to take ove$
        vrrp_instance VI_01 {
        state MASTER
        interface enp8s0
        virtual_router_id 51
        priority 102
        # The virtual ip address shared between the two loadbalancers
        virtual_ipaddress {
        192.168.11.96/24
        }
        track_script {
        check_haproxy
        }}


Add the below configuration on the slave node.

.. code-block:: shell

       global_defs {
        # Keepalived process identifier
        lvs_id haproxy_DH_passive
        }
        # Script used to check if HAProxy is running
        vrrp_script check_haproxy {
        script "killall -0 haproxy"
        interval 2
        weight 2
        }
        # Virtual interface
        # The priority specifies the order in which the assigned interface to take over in a failover
        vrrp_instance VI_01 {
        state SLAVE
        interface ens33
        virtual_router_id 51
        priority 100
        # The virtual ip address shared between the two loadbalancers
        virtual_ipaddress {
        192.168.11.96/24
        }
        track_script {
        check_haproxy
        }}

Restart Keepalived.

.. code-block:: shell

       service keepalived start

Now let’s configure HAProxy on both instances. You will have do the below steps on master node as well as slave node.

.. code-block:: shell

       sudo nano /etc/default/haproxy

set the property ENABLED to 1.

.. code-block:: shell

       sudo nano /etc/haproxy/haproxy.cfg