Software
========

    The Software part of the Hawk Project is split into
    three main parts, two of which reside in the same Repository
    on GitHub and one having its own separate Repository.

    - `HAWK Flightcode <https://github.com/AetherAerospace/hawk-flightcode>`_
    - `HAWK Groundcode <https://github.com/AetherAerospace/hawk-groundcode>`_

Flightcode
^^^^^^^^^^

    This refers to the complete onboard logic that is flashed on the
    onboard ESP32-Microcontroller.

LoRa Transmit/Receive (lTRX)
""""""""""""""""""""""""""""

    This Project uses LoRa to handle the communication between air and ground.
    For convenience we use the excellent prebuilt LoRa Library by
    `sandeepmistry <https://github.com/sandeepmistry/arduino-LoRa>`_

Waypoint Handling
"""""""""""""""""

    @TODO

PID-Controller
""""""""""""""

    @TODO

Onboard Telemetry
"""""""""""""""""

The onboard telemetry is designed to function as a failsafe to get data if the aircraft crashes or the connection is lost.
The code (Telemetry.cpp) get sensors and position data to generates a CSV file written to an SD-Card module.
The class FlightData is used to generate an object ad then pushed to a vector for temporary storage.

.. code-block::

    class FlightData {
        public:
            int time;
            int longitude;
            int latitude; 
            int altitude;
            int roll;
            int pitch;
            int yaw;
    };

In writeToCSV the file is created. The name must have a specific syntax to be used in Tacview (“Aircraft type” (“Callsign”) [“displayed color”].csv).
After iterating through the vector and writing to the csv, the file is closed and saved.

.. code-block::

    file.open("AAE-1 (Hawk) [White].csv", std::ios::app);
        
        //loop through the vector and write to csv file
        for (int i = 0; i < data.size(); i++) {
            file << data[i].time << "," << data[i].longitude << "," << data[i].latitude << "," << data[i].altitude << "," << data[i].roll << "," << data[i].pitch << "," << data[i].yaw << "\n";
    }

    file.close();

Groundcode - ESP32-Onboard
^^^^^^^^^^^^^^^^^^^^^^^^^^

    This is housed in a subfolder in the main `Groundstation Repository
    <https://github.com/AetherAerospace/hawk-groundcode>`_
    The complete logic for the ESP32-Microcontroller Groundstation.

Packet Types
""""""""""""

    @TODO

Groundcode - WebControlPanel (WCP)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

    This is the main Interface that communicates with the
    ESP32-Microcontroller Groundstation.

Interface
"""""""""

    The WCP visualizes route, flight- and no-flight-zones. Usings the buttons on the left side of the screen, you can upload a route to the aircraft, initiate launch, or abort the mission with the FTS (Flight-Terminate-System). Signal-strength and the picked waypoints are also shown to maintain transparency for the operator and help to complete the last pre-flight check. The WCP is using a map to visualize the route, flight- and no-flight-zones.

    .. image:: /img/software/Interface/aether_web_control_pannel.png
        :align: center

    By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.

    .. image:: /img/software/Interface/waypoints.png
        :align: center
    
    By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.


GPS Waypoint Handling
"""""""""""""""""""""

    Set waypoints are read by using draw.getAll() and further processed by parsing the waypoints to generate a GPX file. 

    .. code-block::

        function generateGPX(coordinates) {
            var gpx = '<?xml version="1.0" encoding="UTF-8"?>';
            gpx += '<gpx version="1.1" creator="AEHTER WCP" xmlns="http://www.topografix.com/GPX/1/1">' + '\n';
            gpx += '<metadata />' + '\n';
            //add the coordinates to the gpx file as waypoints
            for (var i = 0; i < coordinates.length; i++) {
                gpx += '<wpt lat="' + coordinates[i][1] + '" lon="' + coordinates[i][0] + '"><name>"'+ i +'"</name></wpt>' + '\n';
            }
            gpx += '</gpx>';
            return gpx;
        }

    The GPX file is then downloaded to the client.

    .. image:: /img/software/GPS_waypoints/download.png
        :align: center

    *Note: That is a temporary solution. Waypoints will be sent directly via API to the ESP-Groundstation.*

Route Calculation
"""""""""""""""""

    @TODO

Route Simulation
""""""""""""""""

    @TODO
