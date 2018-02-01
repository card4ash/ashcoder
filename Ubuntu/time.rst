Time Synchronization on Ubuntu 
=================================

Basic Time Commands
********************

.. code-block :: shell

    date

.. code-block :: shell

    timedatectl list-timezones

.. code-block :: shell

    sudo timedatectl set-timezone Asia/Dhaka

.. code-block :: shell 

    timedatectl

.. code-block :: shell

    sudo dpkg-reconfigure tzdata

Configure NTP Servers
***********************

.. code-block :: shell

    apt-get install ntp

.. code-block :: shell

     sudo nano /etc/ntp.conf

.. code-block :: shell

     ntpq -p

.. code-block :: shell

    service ntp restart