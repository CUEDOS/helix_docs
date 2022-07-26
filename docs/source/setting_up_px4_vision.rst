The hardware being used for this demonstration is the `PX4 Vision
Autonomy Development
Kit <https://docs.px4.io/v1.12/en/complete_vehicles/px4_vision_kit.html>`__.
This platform provides a ready to fly quadcopter with a pixhawk 4 flight
controller and an on board UP Core companion computer. For the purposes
of the demonstration some additional setup is required which is detailed
in this document.

Hardware Setup
--------------

The only hardware setup required is to install the TBS Crossfire Nano
Rx.

Receiver Installation
~~~~~~~~~~~~~~~~~~~~~

1. Solder Header pins to ground, 5V and CH1 pins.
2. Connect Rx header pins to port marked DSM/SBUS RC on Pixhawk 4 with
   included cable.
3. Bind Rx to radio.
4. Run crossfire lua script on radio, select Nano Rx and change CH1 to
   sbus.

WiFi Telemetry Setup
~~~~~~~~~~~~~~~~~~~~

By default the PX4 Vision Autonomy Devlopment Kit comes with an ESP8266
WiFi Telemetry module which is set to access point mode. This will
broadcast a network which the ground station can connect to to receive
telemetry. For the purposes of the demonstration the ESP8266 module
needs to be in station mode instead. When in station mode the module
will connect to an existing wifi network and telemetry can be passed to
the ground control station through the network. This means one ground
station can receive telemetry from multiple drones through the same WiFi
network. Though QGroundcontrol offers a setup inteface for the module it
is not functional, therefore the browser setup interface must be used.

1. Connect to the wireless access point created by the ESP8266 module,
   in the case of the PX4 Vision Autonomy Development Kit the SSID and
   password are both pixhawk4.
2. Navigate to `http://192.168.4.1/ <http://192.168.4.1/>`__ in a
   browser.
3. click on 'Setup'.
4. Set the 'WiFi Mode' to 'Station', set the 'WiFi Channel' to 6, enter
   the SSID and password of the existing wifi network into 'Station
   SSID' and 'Station Password', all other settings can be left as
   default. Save these settings and reboot the drone.
5. The WiFi Module should now reboot in station mode and connect to the
   existing network, telemetry should now be visible in port 14550 in
   QGroundcontrol.
6. Assign a static IP to the WiFi Telemetry module following the
   instructions in the `Setting up the Local
   Network <https://github.com/CUEDOS/cascade-demo/wiki/Setting-up-the-Local-Network#assign-static-ip-addresses>`__
   page. The setup page can now be accessed by navigating to the new
   static IP in the browser.

Software Setup
--------------

Both the Pixhawk and the UP Core companion computer require some
software setup for the demonstration.

Ubuntu Installation on UP Core
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

1. Follow the tutorial on the `Ubuntu
   website <https://ubuntu.com/tutorials/create-a-usb-stick-on-ubuntu#1-overview>`__
   to create a bootable Ubuntu 20.4 LTS USB Stick.
2. Connect a keyboard, mouse and monitor to the UP Core and insert the
   USB stick then connect the computer to power.
3. The computer will boot and give the option to try Ubuntu or install
   it, select install and follow the on screen setup process. Make sure
   to select 'Minimal Installation' and 'Erase disk and install Ubuntu'.

Enabling use of the serial port in Ubuntu (IMPORTANT)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

By default Ubuntu includes a modem manager which blocks the use of the
serial port to communicate with the Pixhawk. This must be removed with
the following terminal commands.

.. code:: sh

   sudo usermod -a -G dialout $USER
   sudo apt-get remove modemmanager -y

Enabling SSH
~~~~~~~~~~~~

By default ssh is not installed on Ubuntu 20.4 LTS. In order to avoid
the need for a monitor keyboard and mouse for each drone in the swarm
during development it can be installed and enabled with the following
terminal commands.

.. code:: sh

   sudo apt update
   sudo apt install openssh-server
   sudo ufw allow ssh
