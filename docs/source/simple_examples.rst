We can send target velocity or target position in offboard mode during experiments to conrol drones in Helixio framework from their onboard computer. The two simple examplse for them are as follows:

1. Offboard Velocity Command
=====================
Target velocity can be sent to each flight controller on each drone by the offboard velocity command of MAVSDK in North-East-Down coordination system.To send this command, you can assign target_velocity in path_following() method as the velocity you want and the flight_mode variable (attribute) as "velocity". Then, the framework gets this target velocity and prepares it for the MAVSDK offboard velocity command. Here is a simple example experiment code to send velocity commands to each drone.
::

    import json
    import flocking
    import math
    import numpy as np
    from string import digits

    def only_numeric(input_string):
        output_string = "".join(c for c in input_string if c in digits)
        return output_string

    class Experiment:
        def __init__(self, id, swarm_telem, experiment_file_path, home_lat, home_lon, home_alt, ref_lat, ref_lon, ref_alt) -> None:
            self.id = id # the id of the drone in its swarm
            self.flight_mode="velocity"
            self.prestart_position=[0, 0, 0]
            self.reference_point=[ref_lat, ref_lon, ref_alt]
            self.home_position=[home_lat, home_lon, home_alt]
            self.load(experiment_file_path) # to load data from json file 

        def load(self, experiment_file_path): # load date from 
            with open(experiment_file_path, "r") as f:
                experiment_parameters = json.load(f)
            self.travel_time = experiment_parameters["travel_time"] # magnitude of speed
            self.pre_start_positions = experiment_parameters["pre_start_positions"] # loading the prestart positions
            self.ready_flag = True # to show everything is loaded

        def get_pre_start_positions(self, swarm_telem, swarm_priorities):

            assigned_pre_start_positions = {}

            for i, agent in enumerate(swarm_priorities):
                if len(self.pre_start_positions) > i:
                    assigned_pre_start_positions[agent] = self.pre_start_positions[i]
                else:
                    # if there isnt enough pre start positions, start from current position
                    assigned_pre_start_positions[agent] = swarm_telem[self.id].position_ned
            self.prestart_position=assigned_pre_start_positions[self.id]
            return assigned_pre_start_positions

        def get_swarm_priorities(self, swarm_telem):
            numeric_ids = {}
            for agent in swarm_telem.keys():
                numeric_ids[agent] = int(only_numeric(agent))

            swarm_priorities = sorted(numeric_ids, key=numeric_ids.get)
            return swarm_priorities

        def path_following(self, swarm_telem, max_speed, time_step, max_accel, mission_start_time): # method to run during the experiment
            mission_time=swarm_telem[self.id].current_time-mission_start_time # swarm_telem[self.id].current_time is the current time which is synchronous (from GPS)
        
            # calculating the target velocity
            if (mission_time<=self.travel_time/2.00):
                target_velocity=[2, 0, 0]
            elif (mission_time<=self.travel_time):
                target_velocity=[-2, 0, 0]
            else:
                target_velocity=[0, 0, 0] # to stop the drone after travel_time
            output_vel = flocking.check_velocity(target_velocity, swarm_telem[self.id], max_speed, 0, time_step, max_accel) # getting the target velocity in its right format
        
            return output_vel # sending the target velocity

In the constructor method, we get the reference and home geodetic positions and call method load() to load data from the JSON file. In mehthod load(), we get prestart positoins and the time duration of the experiment. Methods get_pre_start_positions() and get_swarm_priorities() are to assign prestart positions and sort the drones in the swarm, respectively. method path_following() is the method running continuously during the experiment (after clicking on Start icon on Helixio GUI). You can write your rules to control the drone in this method. 

Note: The variable we use as the current time is swarm_telem[self.id].current_time, which is from GPS that is a synchronous source of time for all of the drones.

To run the experiment, At first, click on Arm icon on Helixio GUI. Then, click on Takeoff to fly the dornes. Afterwards, click on Start to make the drones go to their prestart positions. Then, click on Start again to start the experiment. After completeing the experiment, you can click on Land, to land them all.
The result of using this experiment with Helixio GUI for three drones and assigning travel_time as 40000000 (in microseconds), prestart positions as [0, 10, -20], [0, 20, -20] and [0, 30, -20] (in the JSON file) in software in the loop simulation is shown below:

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helix_docs/blob/main/img/examp_velocity.png"></p>
 
The above figure has been obtained using ulog_visulaiser.py in Post_flight_tools folder and ulogs of drones from SITL. The file of this experiment is available at:

 https://github.com/CUEDOS/helixio_V2/blob/experiment_2/helixio/example_velocity.py
 
2. Offboard Position Command
=====================
Target position can be sent to each flight controller on each drone by the offboard position command of MAVSDK in North-East-Down coordination system.To send this command, you can assign target_position in path_following() method as the positioin you want and the flight_mode variable (attribute) as "position". Bear in mind that the point should be expressed with respect to the reference point (not home point). Then, the framework gets this target position and prepares it for the MAVSDK offboard position command.  Here is a simple example experiment code to send position commands to each drone.
::

    import json
    import flocking
    import math
    import numpy as np
    from string import digits

    def only_numeric(input_string):
        output_string = "".join(c for c in input_string if c in digits)
        return output_string

    class Experiment:
        def __init__(self, id, swarm_telem, experiment_file_path, home_lat, home_lon, home_alt, ref_lat, ref_lon, ref_alt) -> None:
            self.id = id # the id of the drone in its swarm
            self.flight_mode="position"
            self.prestart_position=[0, 0, 0]
            self.reference_point=[ref_lat, ref_lon, ref_alt]
            self.home_position=[home_lat, home_lon, home_alt]
            self.load(experiment_file_path) # to load data from json file 

        def load(self, experiment_file_path): # load date from 
            with open(experiment_file_path, "r") as f:
                experiment_parameters = json.load(f)
            self.travel_time = experiment_parameters["travel_time"] # magnitude of speed
            self.pre_start_positions = experiment_parameters["pre_start_positions"] # loading the prestart positions
            self.ready_flag = True # to show everything is loaded

        def get_pre_start_positions(self, swarm_telem, swarm_priorities):

            assigned_pre_start_positions = {}

            for i, agent in enumerate(swarm_priorities):
                if len(self.pre_start_positions) > i:
                    assigned_pre_start_positions[agent] = self.pre_start_positions[i]
                else:
                    # if there isnt enough pre start positions, start from current position
                    assigned_pre_start_positions[agent] = swarm_telem[self.id].position_ned
            self.prestart_position=assigned_pre_start_positions[self.id]
            return assigned_pre_start_positions

        def get_swarm_priorities(self, swarm_telem):
            numeric_ids = {}
            for agent in swarm_telem.keys():
                numeric_ids[agent] = int(only_numeric(agent))

            swarm_priorities = sorted(numeric_ids, key=numeric_ids.get)
            return swarm_priorities

        def path_following(self, swarm_telem, max_speed, time_step, max_accel, mission_start_time): # method to run during the experiment
            mission_time=swarm_telem[self.id].current_time-mission_start_time # swarm_telem[self.id].current_time is the current time which is synchronous (from GPS)
            # calculating the target position
            if (mission_time<=self.travel_time/2.00):
                target_position=[40 + self.prestart_position[0], 0 + self.prestart_position[1], -20]
            elif (mission_time<=self.travel_time):
                target_position=[0 + self.prestart_position[0], 0+ self.prestart_position[1], -20]
            else:
                target_position=[0 + self.prestart_position[0], 0 + self.prestart_position[1], -20]
            output_pos = flocking.check_position(target_position, swarm_telem[self.id], max_speed, 0, time_step, self.reference_point, self.home_position) # getting the target velocity in its right format
            return output_pos # sending the target velocity
            
The methods of this experiment file are the same as previous one except the commands in method path_following() that we editted to change the mission and commands we send to the drones.
The result of using this experiment with Helixio GUI for three drones and assigning travel_time as 40000000 (in microseconds), prestart positions as [0, 10, -20], [0, 20, -20] and [0, 30, -20] (in the JSON file) in software in the loop simulation is shown below:

.. raw:: html

   <p align="center"><img src="https://github.com/CUEDOS/helix_docs/blob/main/img/examp_position.png"></p>

The above figure has been obtained using ulog_visulaiser.py in Post_flight_tools folder and ulogs of drones from SITL. The file of this experiment is available at:

https://github.com/CUEDOS/helixio_V2/blob/experiment_2/helixio/example_position.py
