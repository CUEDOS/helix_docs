During the demonstration the drones communicate with each other through
a local wireless network. The network needs to be set up so that the
DHCP server assigns static IP addresses to the drones and the ground
station. This is necessary for routing the MQTT messages which are used
to pass telemetry data around the network.

Router Configuration
--------------------

The router used in this project is manufactured by TPLink however the
process should be similar for other routers.

Assign Static IP Addresses
~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Navigate to the router configuration page in a browser, usually found
   at `http://192.168.0.1 <http://192.168.0.1>`__.
2. Login with the details found on the router.
3. Navigate to the 'Advanced' tab and then to 'DHCP Server'
4. Under 'Address Reservation' click 'Add' and then enter the MAC
   address of the drone or ground station and a permanent static IP
   address.

Ground Station Network Settings
-------------------------------

The ground station is connected via ethernet to the router and this
connection can be configured to enable the ground station to access the
internet through a seperate wifi connection while still being able to
communicate on the local network. It is assumed the ground station is
running Ubuntu 20.4 LTS

1. Navigate to the 'Network' tab in the ubuntu settings and click the
   gear next to 'Ethernet'.
2. Navigate to the 'IPv4' tab and select 'Automatic (DHCP)' for 'IPv4
   Method'.
3. Check 'Use this connection only for resources on its network' at the
   bottom of the page.
