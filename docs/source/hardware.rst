Hardware
========

    The Hardware part resides in one main repo and mostly contains the finished
    stl's,3mf and step files directly out of Blender, OnShape and Fusion360.

    - `HAWK Hardware <https://github.com/AetherAerospace/hawk-hardware>`_

Design Considerations
---------------------

    Our designs are based on current and past experiences we made with
    flying objects and aerodynamic design.

Size
----

    The size is mostly determined by the decision to use two engines and the rotor-circumference.

Overall size
^^^^^^^^^^^^^^^^

    :Width:  	
        1740.868mm  1.741m

        .. image:: /img/hardware/overall/overall_front.jpg
            :align: center
        
    :Height:	
        276.50mm   0.277m  

        .. image:: /img/hardware/overall/overall_height.jpg
            :align: center

    :Length: 	
        780.529mm   0.781 
        
        .. image:: /img/hardware/overall/overall_side.jpg
            :align: center
    
Body
^^^^

    :Engine-Block:
        :Overall length:
            780.529mm

            .. image:: /img/hardware/engine_block/engine_block_side.JPG
                :align: center                    
        
        :Nose length:
            113.341mm
            
            .. image:: /img/hardware/engine_block/nose_side.JPG
                :align: center
        
        :Nozzle length:
            122.846mm
            
            .. image:: /img/hardware/engine_block/nozzle_side.JPG
                :align: center

    :Engines:
        :Rotor-Circumference: 
            88mm
        :Engine-Block:
            90mm
        :Battery-Gap:
            24mm
        :Deck:
            136mm    
        :Wing-Mound-Surface:
            72mm

        .. image:: /img/hardware/engine_block/engine.JPG
            :align: center

Tail
^^^^

    :Overall length:
        335.00mm

         .. image:: /img/hardware/tail/overall_length.JPG
            :align: center   
        
    :Overall height:
        258.00mm

         .. image:: /img/hardware/tail/overall_height.JPG
            :align: center  

    :Overall width:
        340.00mm

         .. image:: /img/hardware/tail/overall_width.JPG
            :align: center  

Wingmounts
^^^^^^^^^^

    :Overall length:
        396.00mm

         .. image:: /img/hardware/wm/overall_length.JPG
            :align: center  

    :Front length:
        226.00mm

         .. image:: /img/hardware/wm/front_length.JPG
            :align: center

    :Middle length:
        10.00mm

         .. image:: /img/hardware/wm/mid_length.JPG
            :align: center

    :Back length:
        160.00mm

         .. image:: /img/hardware/wm/back_length.JPG
            :align: center

    :Overall width:
        51.081mm

         .. image:: /img/hardware/wm/overall_width.JPG
            :align: center


Weight
------

    Will be added after printing and assembly is finished. Estimates based on experiences and slicer previews lie below 1.5 kg. 

Aerodynamics
------------

Aerodynamic Concepts
^^^^^^^^^^^^^^^^^^^^

    :Wing configuration:
        :Number:
            Monoplane 
            1)

        :Positioning:
            Mid wing
            1)

        :Bracing:
            Potentially wire braced, come back after assembly.

        :Aspect ration:
            Moderate
            2)

        :Chord variation along span:
            Tapered
            2)

        :Wing sweep:
            Swept
            2)

        :Dihedral/Anhedral:
            Dihedral, 5° overall, Dihedral tips and winglets
            1)

        :Body form:
            Blended body
        
        1
         .. image:: /img/aerodynamics/wings_front.JPG
            :align: center

        2
         .. image:: /img/aerodynamics/wings_top.JPG
            :align: center

        
    
    :Wing-Wash:
        The outer parts of the wings are tilted downwards. This allows for better roll-controls in high AoA (Angle of Attack) or stalls.

         .. image:: /img/aerodynamics/wingwash.JPG
            :align: center

    :Elevator positioning:
        Lorem Ipsum


Parts
-----

3D-printed Parts
^^^^^^^^^^^^^^^^

    :Engine-Block:
        - Engine-House Base
        - Engine-House Top
        - Engine Body
        - Roof
        - Tail-Connector Cable Cover
        - Nose
        - Nose Bottom
        - Nose Roof
        - Nozzle

    |

    :Tail:
        - Tail
        - Tail-Connector
        - Tail-Bridge
        - Tail Base
        - Tail-Connector Fin
        - Tail Fin
        - Control-Surface Tail
        - Control-Surface Elevator left
        - Control-Surface Elevator right

    |

    :Wing Mounds:
        - Wing-Mount Front left
        - Wing-Mound Middle left
        - Wing-Mound Back left

        |

        - Wing-Mount Front right
        - Wing-Mound Middle right
        - Wing-Mound Back right

    |

    :Wings:
        - Wing-Base Front left
        - Wing-Base Back left
        - Wing-Middle Front left
        - Wing-Middle Back left
        - Wing-End Front left
        - Wing-End Front back
        - Winglet left
        - Control-Surface Aileron left

        |

        - Wing-Base Front right
        - Wing-Base Back right
        - Wing-Middle Front right
        - Wing-Middle Back right
        - Wing-End Front right
        - Wing-End Front right
        - Winglet right
        - Control-Surface Aileron right

    :Struts:
        - Strut Inner Front left
        - Strut Inner Back left
        - Strut Outer Front left
        - Strut Outer Front left 2
        - Strut Outer Back left
        - Strut Outer Back left 2
  
        |

        - Strut Inner Front back 
        - Strut Inner Back back 
        - Strut Outer Front back 
        - Strut Outer Front  back 2
        - Strut Outer Back back 
        - Strut Outer Back  back 2


Electronics
^^^^^^^^^^^
    - 2x ESP32 with LoRa integrated
    - 2x Aikon 30A ESC 2-4S
    - 2x T-Motor F1507 3800KV
    - 1x Generic 3S LiPo
    - 4x Generic Servo



Previous builds
---------------

Concept Trailer
^^^^^^^^^^^^^^^

    AETHER HAWK started with a concept trailer build and animated in early september 2022. The idea behind this design was reusing the old AETHER HEAVY rocket as engine and build the aircraft around it. This design was completely modeled and designed in Blender (except for the AETHER HEAVY Rocket itself). Even thou this design was purely thought to be an inspiration and motivation it already had some aerodynamic decisions implemented that ended up being reused in the current design. 

    Watch the trailer here https://www.youtube.com/watch?v=ejGdx6ON9bw

