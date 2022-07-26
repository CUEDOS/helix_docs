There are two conditions triggering failsafe mode. The conditions are as
follows:

1. Connection between the onboard computer and the flight controller is
   lost (loss of offboard connection)
2. Telemetry connection between the drone and the Ground station is lost
   (loss of MQTT connection)

loss of offboard connection is more dangerous than loss of MQTT
connection, because in the case of loosing offboard connection we do not
have a computer on the drone to run our emergency code. Therefore, the
safest and only decision we can make is to land the drone. We should
keep in mind that landing on slopes may be challenging for the drone
losing its onboard computer.

The frequency of loosing offboard connection is so rare because it uses
cable connection. But the frequency of loosing MQTT connection is often
because the connection protocol is WiFi.

+-------------+-------------+-----------+-------------+-------------+
| Type of     | Cause       | Frequency | Measure     | Challenge   |
| connection  |             |           |             | of Measure  |
| loss        |             |           |             |             |
+=============+=============+===========+=============+=============+
| loss of     | loss of     | rarely    | landing     | landing on  |
| offboard    | cable       |           | immediately | slopes      |
|             | connection  |           |             |             |
+-------------+-------------+-----------+-------------+-------------+
| loss of     | loss of     | often     | triggering  | not knowing |
| MQTT        | WiFi        |           | return home | altitude of |
|             | connection  |           | after a few | other       |
|             |             |           | seconds     | drones      |
+-------------+-------------+-----------+-------------+-------------+
