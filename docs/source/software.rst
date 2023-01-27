********
Software
********

    The Software part of the Hawk Project is split into
    three main parts, two of which reside in the same Repository
    on GitHub and one having its own separate Repository.

    - `HAWK Flightcode <https://github.com/AetherAerospace/hawk-flightcode>`_
    - `HAWK Groundcode <https://github.com/AetherAerospace/hawk-groundcode>`_

ESP32 Codebase
==============
*~ teppanf*

    This Project uses LoRa to handle the communication between air and ground.
    For convenience we use the excellent prebuilt LoRa Library by
    `sandeepmistry <https://github.com/sandeepmistry/arduino-LoRa>`_

This refers to the complete onboard logic that is flashed on the
onboard ESP32-Microcontroller. Flightcode and Groundcode are to be
seen as sepearte codebases, but share basically the same LoRa architecture
and functionality.

LoRa Transmit/Receive (lTRX)
----------------------------

    lTRX short for **L**\oRa **T**\ransmit **R**\ecieve is the component that handles the packet
    processing and also the packet crafting/sending.

The link code adheres to a strict packet formatting guideline which
looks something like this:

.. code-block::

    ------------------------------------------------
    | DST_ID | SRC_ID | MSG_LENGTH | PKT_ID | DATA |
    ------------------------------------------------

- DST_ID
    The Receiver ID
- SRC_ID
    The Transmitter ID
- MSG_LENGTH
    The Length of the following Data
- PKT_ID
    The Packet Type ID
- DATA
    The Actual Packet Data

We always test for two basic things to match before we process any
packet any further:

.. code-block::

    if (MSG_LENGTH != ACTUAL_LENGTH) {
        return;
    }

    if (DST_ID != LORA_LOCAL_ID) {
        return;
    }

lTRX 1.0
^^^^^^^^

The first version used a lot of different packet types that were send one after another. 
The whole code got weird really fast and overall there wasn't really any way in which we
could receive telemetry downlink without the whole link failing.

Here is a quick example of how that looked like:

.. code-block::

    // craft control packets to be handled by receiver
    void craftCTL(int t) {
        switch (t) {
            case 31:
                sendLoRa(t,"")
                break;
            case 32:
                sendLoRa(t,"")
                break;
            case 33:
                sendLoRa(t,"")
                break;
    }

    // decoder function for lTRX packets
    void declTRX(int msgType, String data, int pktRS, float pktSNR) {
        lastPacketRSSI = pktRS;
        lastPacketSNR = pktSNR;
        switch (msgType) {
            case 10 ... 19:
                // LINK
                break;
            case 20 ... 29:
                // GPS
                break;
        }
    }

    // lTRX main control loop
    void lTRXctl() {
        crCL = millis();
        if (crCL - prCL > LTRX_DELAY) {
            prCL = crCL;
            // main control is always sent
            craftCTL(31);
            // every 4 packets
            if (pktCnt % 4 == 0) {
                craftCTL(32);
                craftCTL(33);
                craftCTL(11);
            }
        } else return;
    }

Following packet types were implemented:

- ID 10-19 handle link quality data
- ID 20-29 handle different GPS data
- ID 31 contain the main controller stick values
- ID 32 contain the left and right shoulder buttons
- ID 33 contain the symbol buttons

lTRX 2.0
^^^^^^^^

The second iteration of lTRX combines most of the seperate IDs into one main
packet which we just refer to as a zero packet.

.. code-block::

    // craft control packets to be handled by receiver
    void lTRXTransmit() {
        if ( pktCnt < LTRX_TELEMETRY_RATE ) {
            sendLoRa(1,
                "A" +
                String(fetchCtrl(0)) +
                "E" +
                String(fetchCtrl(1)) +
                "R" +
                String(fetchCtrl(2)) +
                "SR" + 
                String(fetchBtn(0)) +
                "SL" +
                String(fetchBtn(1)) +
                "Q"
            );
            pktCnt++;
        } else {
            // telemetry request packet
            sendLoRa(100,"REQ");
            // update REQ timestamp
            pastMillRespThresh = millis();
            // requested ACK, set wait bool
            waitForACK = true;
            // reset packet count
            pktCnt = 0;
        }
    }

Now let's explain what's going on here. This function is called by the main loop
which we will also take a look at in just a moment.
The only thing going on here is checking if we are currently in a state where we shoulder
be expecting to get a telemetry answer from our aircraft, or if we are just shooting our
main packets at it to keep everything operational.
The `else` block defines that if we are at a time when we need to get telemety, we put LoRa
into receive mode and send a request packet with *ID 100*.

.. code-block::

    // parse actual packet based on ID
    void handleStream(int t, String d) {
        if ( t != 100 ) {
            // parse main control values
            aerMain[0] = d.substring( 1, d.indexOf("E") ).toInt();
            aerMain[1] = d.substring( (d.indexOf("E") + 1), d.indexOf("R") ).toInt();
            aerMain[2] = d.substring( (d.indexOf("R") + 1), d.indexOf("SL") ).toInt();
            // parse button values
            btnMain[0] = d.substring( (d.indexOf("SL") + 2), d.indexOf("SR") ).toInt();
            btnMain[1] = d.substring( (d.indexOf("SR") + 2), d.indexOf("Q") ).toInt();
            ++pktCnt;
        } else {
            // we need to send ACK
            isACKLoop = true; 
            // reset packet counter
            // so next packets will be ACK
            pktCnt = 0;
        }
    }

Now this part in the flightcode, which just slightly differs from ground will look at packets
and go into the else block if it receives the earlier mentioned *ID 100* request packet.
It then puts the aircraft into transmit mode and will send an answer packet with telemetry data back.

.. code-block::

    // lTRX main control loop
    void lTRXctl() {
        if (millis() - pastMillTRX > LTRX_DELAY) {
            if ( 
                !isACKLoop  ||
                pktCnt > LTRX_ACK_PACKETS
            ) {
                // assume ack loop done
                isACKLoop = false;
                // recieving
                LoRaRXM();
            } else {
                // set transmit
                LoRaTXM();
                // send ACK packet with telemetry
                lTRXTransmit();
            }
            pastMillTRX = millis();
        } else return;
    }

Looking at the control loop, we can see that it doesn't only send it once but actually at an earlier
defined count, which we set as 3. This gives us some redundancy while also keeping the aircraft in transmit
state for a very short amount of time. After sending 3 answer packets it will go back to receiving mode. This
keeps the link nice and fast.

.. code-block::

    // lTRX main control loop
    void lTRXctl() {
        // send response after delay
        if (millis() - pastMillTRX > LTRX_DELAY) {
            if ( 
                !waitForACK || 
                millis() - pastMillRespThresh > LTRX_RESPONSE_THRESHOLD 
            ) {
                // clear wait
                waitForACK = false;
                // transmitting
                LoRaTXM();
                lTRXTransmit();
            } else {
                // set recieve
                LoRaRXM();
            }
            pastMillTRX = millis();
        } else return;
    }

The main ground loop looks quite similar. It will send the request packet and the wait for an answer, but only
according to a specific pre-defined threshold. If it gets an answer within the threshold time, it will continue normal
operation. Otherwise the threshold is the absolute maximum amount of time it will wait for an answer before continuing 
with the normal control packets. Again, this is to keep the link fast but to also give the aircraft a little bit of time
to send telemetry data back.

Web Control Panel
=================
*~ birnbacm*

    This is the main Interface that communicates with the
    ESP32-Microcontroller Groundstation.

Interface
---------

The WCP visualizes route, flight- and no-flight-zones. Usings the buttons on the left side of the screen, you can upload a route to the aircraft, initiate launch, or abort the mission with the FTS (Flight-Terminate-System). Signal-strength and the picked waypoints are also shown to maintain transparency for the operator and help to complete the last pre-flight check. The WCP is using a map to visualize the route, flight- and no-flight-zones.

.. image:: /img/software/Interface/aether_web_control_pannel.png
    :align: center

By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.

.. image:: /img/software/Interface/waypoints.png
    :align: center

By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.

GPS Waypoint Handling
---------------------

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

Aether Mission Control
======================
*~ birnbacm*

.. image:: /img/groundcode/missioncontrol.png
            :align: center

AETHER Mission Control is a desktop application that allows you to control the AETHER drone. It is written in Python and uses the PyQt5 library for the GUI.

It is split into 3 main parts:

- The mission control module
- The main application file
- The setup file

Main Application
----------------

The main application file is mission-control.py. It contains the following code and is self explanatory:

.. code-block:: python

    #import main function from main.py in the folder mission-control

    from missioncontrol.map import main

    #call the main function
    if __name__ == "__main__":
        main()

Mission Control Module
----------------------

The mission control module is the main part of the application. It contains the following files:

- map.py - contains the main code of the application
- token.py - contains the mapbox token for the map
- toserial.py - this module reads and writes to the serial port with multithreading

Map.py
^^^^^^

map.py contains the main gui code for the application. It contains a main function ``main()`` that creates the gui and the main window class ``MainWindow``. The main window class contains the following functions:

- ``__init__()`` - initializes the main window and creates the gui. As part of the initialization, it also creates the map and three buttons to control the drone.

.. code-block:: python

    def __init__(self):
        #Window (...)
        #Map (...
        #Buttons (...)
        #see map.py for full code


- ``def on_button1_clicked`` - this function is called when the first button is clicked. It sends the waypoint data to the ground station via the toserial module. The ground station will then read the data and upload it to the drone.

.. code-block:: python

    def on_button1_clicked(self):
        self.map_view.page().runJavaScript("getLine()", self.on_markers_retrieved)


- ``on_markers_retrieved`` - this function scrapes the data from a predefined JavaScript function in the mapbox map. It is called inside the ``on_button1_clicked`` function and returns the waypoint json data.

.. code-block:: python

    #generate a json object with the points of the drawn line and send it to the drone
    def on_markers_retrieved(self, markers_data):
    markers = markers_data
    #extract the points from the returned data
    markersExtracted = markers['features'][0]['geometry']['coordinates']
    #print the returned points in the text output
    self.text.setText(str(markersExtracted))
    #save the points in a json file
    with open("markers.json", "w") as f:
        json.dump(markers, f)

    #upload the points to the drone
    self.upload_to_drone(markersExtracted)


- ``upload_to_drone`` > this function uploads the waypoint data to the drone. It is called inside the ``on_markers_retrieved`` function. It uses the toserial module to write the data to the serial port. To circumvent errors, it uses a try catch block to catch errors and close the serial port in case of an error. It also uses multithreading to read and write to the serial port at the same time.

.. code-block:: python

    Python
    #upload the json to the drone via serial communication
    def upload_to_drone(self, markers):
    print("\033[92m" + "UPLOAD TO DRONE" + "\033[0m")
    print(markers)
    #surround the communication with a try catch block to catch errors
    try:
        #send the points to the drone via serial communication
        #listen to the serial port
        read_thread = threading.Thread(target=read_from_serial)
        #send the points to the serial port
        write_thread = threading.Thread(target=write_to_serial, args=(markers,))
        #start the threads
        read_thread.start()
        write_thread.start()
        #wait for the threads to finish
        read_thread.join()
        write_thread.join()
        #close the serial port
        close_serial()
    except Exception as e:
        print("\033[91m" + "ERROR: " + str(e) + "\033[0m")
        #close the serial port
        close_serial()


- ``def on_button2_clicked`` - this function is called when the second button is clicked. It sends the start command to the ground station via the toserial module. The ground station will then read the data and upload it to the drone.

.. code-block:: python

    #send a command to the drone to start the mission
    def on_button2_clicked(self):
    print("\033[92m" + "SEND IT!" + "\033[0m")
    #surrond the communication with a try catch block to catch errors
    try:
        #send the command to the drone to start the mission
        #listen to the serial port
        read_thread = threading.Thread(target=read_from_serial)
        #send the command "SEND_IT" to the serial port
        write_thread = threading.Thread(target=write_to_serial, args=("SEND_IT",))
        #start the threads
        read_thread.start()
        write_thread.start()
        #wait for the threads to finish
        read_thread.join()
        write_thread.join()
        #close the serial port
        close_serial() 

    except Exception as e:
        print("\033[91m" + "ERROR: " + str(e) + "\033[0m")
        #close the serial port
        close_serial()


- ``def on_button3_clicked`` - this function is called when the third button is clicked. It sends the stop command to the ground station via the toserial module. The ground station will then read the data and upload it to the drone.

.. code-block:: python
    
    #send a command to the drone to abort the mission
    def on_button3_clicked(self):
    print("\033[91m" + "ABORT!" + "\033[0m")
    #surrond the communication with a try catch block to catch errors
    try:
        #send the command to the drone to abort the mission
        #listen to the serial port
        read_thread = threading.Thread(target=read_from_serial)
        #send the command "ABORT" to the serial port
        write_thread = threading.Thread(target=write_to_serial, args=("ABORT",))
        #start the threads
        read_thread.start()
        write_thread.start()
        #wait for the threads to finish
        read_thread.join()
        write_thread.join()
        #close the serial port
        close_serial() 
    except Exception as e:
        print("\033[91m" + "ERROR: " + str(e) + "\033[0m")
        #close the serial port
        close_serial()

token.py
^^^^^^^^

``token.py`` contains the mapbox token for the map. It is used in the map.py file to create the map.

When you create your own mapbox account, you will get your own token. You can then add your token via the setup script or manually in the token.py file. 
Note that the token is not included in the repository for security reasons. 
Also note that the file has a specific structure:

.. code-block:: python

    MAP_TOKEN = 'YOUR-TOKEN-HERE'

toserial.py
^^^^^^^^^^^

- ``toserial.py`` contains the functions to write and listen to the serial port. It is used in the main.py file to send the waypoint data to the drone. 
    Write and read functions are implemented to run multithreaded.

- ``choose_serial``
    At first, the os is checked to determine the serial port. This check is necessary because the serial port is different on Windows and Linux. It is checked every time the functions are called to make sure the correct port is used.

.. code-block:: python

    #choose the serial connection based on the OS
    def choose_serial():
    #if the running OS is windows use the windows serial connection
    if os.name == 'nt':
        #find the COM port of the serial connection and return it
        for i in range(256):
            try:
                #try to open the COM port as string
                s = serial.Serial("COM" + str(i), 115200)
                s.close()
                #output the COM port number and make the background of the print green
                print("\033[92m" + "COM port found! COM port: " + str(i) + "\033[0m")
                return serial.Serial("COM" + str(i), 115200)
            except serial.SerialException:
                pass
        #if no COM port is found, return an error and make the background of the print red
        print("\033[91m" + "ERROR: No COM port found!" + "\033[0m")

    #if the running OS is linux use the linux serial connection
    elif os.name == 'posix':
        #find the ttyUSB port of the serial connection and return it
        for i in range(256):
            try:
                #try to open the ttyUSB port as string
                s = serial.Serial("/dev/ttyUSB" + str(i), 115200)
                s.close()
                #output the ttyUSB port number and make the background of the print green
                print("\033[92m" + "ttyUSB port found! ttyUSB port: " + str(i) + "\033[0m")
                return serial.Serial("/dev/ttyUSB" + str(i), 115200)
            except serial.SerialException:
                pass

- ``read_from_serial``
    This function reads the data from the serial port. It is used to check if the command has been received by the ground station.

    .. code-block:: python

        # Define a function for reading from the serial connection
        def read_from_serial():
        #set the serial connection type based on the OS
        ser = choose_serial()

        global stop
        while not stop:
            # Read data from the serial connection
            data = ser.readline()

            # Print the data to the terminal
            print(data)

            # Check if the command "success" has been received
            if data == b'success\n':
                # Signal to the threads to stop
                stop = True


- ``write_to_serial``
    This function writes the data to the serial port. It is used to send commands to the ground station. 

    .. code-block:: python

        # Define a function for writing to the serial connection
        def write_to_serial(send_data):
        #set the serial connection type based on the OS
        ser = choose_serial()

        global stop
        while not stop:
            #send the send_data to the drone
            ser.write(send_data.encode())
            #after sending the data, send the string "complete" to the drone
            ser.write("complete".encode())


- ``close_serial``
    This function closes the serial port. It is used to close the serial port after the threads have finished. 

    .. code-block:: python
    
        # Define a function for closing the serial connection
        def close_serial():
        #set the serial connection type based on the OS
        ser = choose_serial()

        # Close the serial connection
        ser.close()


- ``multithreading``
    Multithreading is used to send the waypoint data to the drone and to listen to the serial port at the same time. 
    Threads are started and joined outside of ``toserial.py`` in the ``main.py`` file. 

.. code-block:: python

        # Create a thread for reading from the serial connection
        read_thread = threading.Thread(target=read_from_serial)
        # Create a thread for writing to the serial connection
        write_thread = threading.Thread(target=write_to_serial)

Setup
-----

The setup script is used to install the required packages and to add the mapbox token to the token.py file. 
The script is called with the following command:

    .. code-block::

    python3 setup-win.py

.. image:: /img/groundcode/Installer-start.png
            :align: center

The installer is based on PyQt5. 
To run the installer, you need to install PyQt5. 
You can install PyQt5 with the following command:
bash
pip install PyQt5


1. The installer checks for basic requirements and installs the required packages. 
Checks:

- Python version
- pip version
- Git version

1. After the checks, it checks if Mission Control is already installed: 

- If Mission Control is already installed, the installer moves to the folder and pulls the latest version from the repository. 
- If Mission Control is not installed, the installer clones the repository.
- If Mission Control is installed, the clone command is skipped. 


1. Next the installer creates a virtual environment and activates it. After that, the required packages are installed. 

    .. code-block:: python  

        #get the current user
        user = getpass.getuser()
        #change the directory to the folder hawk-groundcode
        os.chdir("C:\\Users\\" + user + "\\hawk-groundcode")
        #create the virtual environment
        os.system("python -m venv env")
        #activate the virtual environment with the command env\Scripts\activate
        os.system("env\Scripts\activate")
        #install the requirements
        os.system("pip install -r requirements.txt")

.. image:: /img/groundcode/Installer-check-done.png
        :align: center

After the checks and the clone/pull, the installer asks for the mapbox token. 
The token is then added to the token.py file following the [defined structure](#tokenpy) 

.. code-block:: python    

    # Execute the dialog event loop
    if dialog.exec_():
        # Create an instance of your application's dialog
        dialog = InputDialog()
        # Show the dialog
        dialog.show()
        # Execute the dialog event loop
        if dialog.exec_():
            #get the current user
            user = getpass.getuser()
            #get the text from the line edit
            text = dialog.text
            #append the text with a prefix and a suffix
            text = "MAP_TOKEN = '" + text + "'"
            filePath = "C:\\Users\\" + user + "\\hawk-groundcode\\missioncontrol\\token.py"
            #create a file and write the text into hawk-groundcode\missioncontrol and name it token.py
            f = open(filePath, "w")
            f.write(text)
            f.close()
            
            #execute the dialog event loop
            dialog = FinishDialog()
            dialog.show()
            if dialog.exec_():
                #start the mission-control.py file
                os.system("python C:\\Users\\" + user + "\\hawk-groundcode\\mission-control.py")
                #close the application
                sys.exit()
        else:
            #close the application
            sys.exit()
    else:
        #close the application
        sys.exit()

.. image:: /img/groundcode/Installer-token.png
        :align: center

After the token is added to the token.py file, the installer shows a short introduction on how to use Mission Control. 
The installer then starts the ``mission-control.py`` file. 

.. image:: /img/groundcode/Installer-done.png
        :align: center
