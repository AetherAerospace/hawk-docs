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

    The size is mostly determined by the decision to use two engines and 
    the rotor-circumference.

Overall size
^^^^^^^^^^^^

:**Width**:

    1740.868mm  1.741m

    .. image:: /img/hardware/overall/overall_front.JPG
        :align: center
    
:**Height**:

    276.50mm   0.277m  

    .. image:: /img/hardware/overall/overall_height.JPG
        :align: center

:**Length**:

    780.529mm   0.781 
    
    .. image:: /img/hardware/overall/overall_side.JPG
        :align: center
    
Body
^^^^

:**Engine-Block**:

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

:**Engines**:

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

:**Overall length**:

    335.00mm

    .. image:: /img/hardware/tail/overall_length.JPG
        :align: center   
    
:**Overall height**:

    *258.00mm*

    .. image:: /img/hardware/tail/overall_height.JPG
        :align: center

:**Overall width**:

    340.00mm

    .. image:: /img/hardware/tail/overall_width.JPG
        :align: center  

Wingmounts
^^^^^^^^^^

:**Overall length**:

    396.00mm

    .. image:: /img/hardware/wm/overall_length.JPG
        :align: center  

:**Front length**:

    226.00mm

    .. image:: /img/hardware/wm/front_length.JPG
        :align: center

:**Middle length**:

    10.00mm

    .. image:: /img/hardware/wm/mid_length.JPG
        :align: center

:**Back length**:

    160.00mm

    .. image:: /img/hardware/wm/back_length.jpg
        :align: center

:**Overall width**:

    51.081mm

    .. image:: /img/hardware/wm/overall_width.JPG
        :align: center

Aerodynamics
------------

Aerodynamic Concepts
^^^^^^^^^^^^^^^^^^^^

:**Wing configuration**:

    - Number

        Monoplane 

    - Positioning

        Mid wing

    - Bracing

        Potentially wire braced, come back after assembly.

    - Aspect ration

        Moderate

    - Chord variation along span

        Tapered

    - Wing sweep

        Swept

    - Dihedral/Anhedral

        Dihedral, 5° overall, Dihedral tips and winglets

    - Body form

        Blended body

        .. image:: /img/aerodynamics/wings_front.JPG
            :align: center

        .. image:: /img/aerodynamics/wings_top.JPG
            :align: center

:**Wing-Wash**:

    The outer parts of the wings are tilted downwards. This allows for better 
    roll-controls in high AoA (Angle of Attack) or stalls.

    .. image:: /img/aerodynamics/wingwash.JPG
        :align: center

Parts
-----

3D-printed Parts
^^^^^^^^^^^^^^^^

.. list-table::
   :widths: 75 25
   :header-rows: 0
   :align: left

   * - **Engine-Block**
     - 

   * - Engine-House Base
     - 60g

   * - Engine-House Top
     - 101g

   * - Roof
     - 
  
   * - Tail-Connector Cable Cover
     -  

   * - Nose
     - 

   * - Nose Bottom
     - 

   * - Nose Roof
     - 11 g

   * - Nozzle
     - 152g

   * - |
     - |

   * - **Tail**
     - 

   * - Tail
     - 

   * - Tail-Connector
     -

   * - Tail-Bridge
     -
    
   * - Tail Base
     - 16g
    
   * - Tail-Connector Fin
     - 
    
   * - Tail Fin
     -
    
   * - Control-Surface Tail
     - 18g
    
   * - Control-Surface Elevator left
     - 51g
    
   * - Control-Surface Elevator right
     - 51g

   * - |
     - |
   
   * - **Wing-Mount**
     -

   * - Wing-Mount Front left
     - 94g
   
   * - Wing-Mound Middle left
     -
   
   * - Wing-Mound Back left - 43
     -
   
   * - Wing-Mount Front right
     - 98g
   
   * - Wing-Mound Middle right
     -
   
   * - Wing-Mound Back right -43
     -

   * - |
     - |
   
   * - **Wings**
     -

   * - Wing-Base Front left
     - 

   * - Wing-Base Back left
     -

   * - Wing-Middle Front left
     -    

   * - Wing-Middle Back left
     -

   * - Wing-End Front left
     -

   * - Wing-End Front back
     -

   * - Winglet left
     - 72g

   * - Control-Surface Aileron left
     -

   * - Wing-Base Front right
     -

   * - Wing-Base Back right
     -

   * - Wing-Middle Front right
     -

   * - Wing-Middle Back right
     -

   * - Wing-End Front right
     -

   * - Wing-End Front right
     -

   * - Winglet right
     - 70g

   * - Control-Surface Aileron right
     -

   * - |
     - |

   * - **Struts**
     -

   * - Strut Inner Front left
     -
  
   * - Strut Inner Back left
     -
   
   * - Strut Outer Front left
     -
  
   * - Strut Outer Front left 2
     -
  
   * - Strut Outer Back left
     -
  
   * - Strut Outer Back left 2
     -
  
   * - Strut Inner Front back 
     -
  
   * - Strut Inner Back back 
     -
 
   * - Strut Outer Front back 
     -
  
   * - Strut Outer Front  back 2
     -
   
   * - Strut Outer Back back 
     -
 
   * - Strut Outer Back  back 2
     -

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

AETHER HAWK started with a concept trailer build and animated in early september 2022. 
The idea behind this design was reusing the old AETHER HEAVY rocket as engine and build 
the aircraft around it. This design was completely modeled and designed in Blender 
(except for the AETHER HEAVY Rocket itself). Even thou this design was purely thought 
to be an inspiration and motivation it already had some aerodynamic decisions implemented 
that ended up being reused in the current design. 

    Watch the trailer here https://www.youtube.com/watch?v=ejGdx6ON9bw
