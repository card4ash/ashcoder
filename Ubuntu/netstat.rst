check opened/closed port on ubuntu
===========================================

There's a few parameters to netstat that are useful for this :

 #. -l or --listening shows only the sockets currently listening for incoming connection.
 #. -a or --all shows all sockets currently in use.
 #. -t or --tcp shows the tcp sockets.
 #. -u or --udp shows the udp sockets.
 #. -n or --numeric shows the hosts and ports as numbers, instead of resolving in dns and looking in /etc/services.
You use a mix of these to get what you want. To know which port numbers are currently in use, use one of these:

.. code-block :: shell

    netstat -atn           # For tcp
    netstat -aun           # For udp
    netstat -atun          # For both
    
In the output all port mentioned are in use either listening for incoming connection or connected to a peer** all others are closed. TCP and UDP ports are 16 bits wide (they go from 1-65535)

** They can also be connecting/disconnecting from the peer.