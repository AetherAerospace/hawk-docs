Software
========

    The Software part of the Hawk Project is split into
    three main parts, two of which reside in the same Repository
    on GitHub and one having its own separate Repository.

    - `HAWK Flightcode <https://github.com/AetherAerospace/hawk-flightcode>`_
    - `HAWK Groundcode <https://github.com/AetherAerospace/hawk-groundcode>`_

ESP32 Codebase
--------------
*~ teppanf*

    This Project uses LoRa to handle the communication between air and ground.
    For convenience we use the excellent prebuilt LoRa Library by
    `sandeepmistry <https://github.com/sandeepmistry/arduino-LoRa>`_

This refers to the complete onboard logic that is flashed on the
onboard ESP32-Microcontroller. Flightcode and Groundcode are to be
seen as sepearte codebases, but share basically the same LoRa architecture
and functionality.

LoRa Transmit/Receive (lTRX)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

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
-----------------
*~ birnbacm*

    This is the main Interface that communicates with the
    ESP32-Microcontroller Groundstation.

Interface
^^^^^^^^^

The WCP visualizes route, flight- and no-flight-zones. Usings the buttons on the left side of the screen, you can upload a route to the aircraft, initiate launch, or abort the mission with the FTS (Flight-Terminate-System). Signal-strength and the picked waypoints are also shown to maintain transparency for the operator and help to complete the last pre-flight check. The WCP is using a map to visualize the route, flight- and no-flight-zones.

.. image:: /img/software/Interface/aether_web_control_pannel.png
    :align: center

By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.

.. image:: /img/software/Interface/waypoints.png
    :align: center

By pressing the line button on the map navigation column, you can draw a line. The circles are representing waypoints. Planned is to display start-/endpoint by using different colors and to implement a loitering functionality.The map icon is used to change the map style from dark to outdoor for a better user experience in lit environments.

GPS Waypoint Handling
^^^^^^^^^^^^^^^^^^^^^

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
