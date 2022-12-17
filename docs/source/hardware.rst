Hardware
========

    The Hardware part resides in one main repo and mostly contains the finished
    stl's,3mf and step files directly out of Blender, OnShape and Fusion360.

    - `HAWK Hardware <https://github.com/AetherAerospace/hawk-hardware>`_

Size
----
*~ simcice*

    The size is mostly determined by the decision to use two engines and 
    the rotor-circumference.

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
----
*~ simcice*

Main
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

Wings
^^^^^

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

:**Aileron Design**:

    231.090mm Overall
    114.86mm Control Surface (right half)

    .. image:: /img/hardware/wings/aileron.PNG
        :align: center

  Simply put, the ailerons are two control-surfaces at the end of each wing. The main function of these control-surfaces is rolling the along it's flight vector. In Flying Wing designs, the ailerons additionally fill the role of the elevators, meaning that they control the pitch of the aircraft.

  In order to move the control surface, a Servo is placed in the 25mm wide cutout that pushes or pulls the control surface. 

:**New wing design**:

    921.713mm Overall
    823.380mm Without control-surfaces

    .. image:: /img/hardware/wings/new_wings.PNG
        :align: center


    The wing itself is designed using two wing-segments as start and end point and the "loft" function. The loft function connects the to points creating the volume of the wing. Changing the start and end profiles of the loft function softens the volume from a straight connection to a more organic shape further enlarging the lift area. 

    .. image:: /img/hardware/wings/loft.PNG
        :align: center


  Aerodynamically speaking, the new wings are identical to the previous ones. They have improved internal struts, adding to the overall stability and ease of construction. 

    .. image:: /img/hardware/wings/struts.PNG
      :align: center
  
  These new struts are build using a function called Booleans. We use a "cutter" to define which areas of the wing should be remail while all others are removed. The boolean calculates which areas of the to objects overlap and remove the rest. The cutter has the shape similar to a comb, giving us straps with equal distance and with. 

    .. image:: /img/hardware/wings/boolean.PNG
      :align: center

  We use the same technique to cut out the front and back connectors. 


Aerodynamics
------------
*~ simcice*

Concepts
^^^^^^^^

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

:**Center of Gravity**:

    Calculating the center of gravity is not effective because the infill of the individual parts is not consistent. In order to solve this problem, the center of gravity can be shifted by moving the battery pack. We can still estimate the center of gravity to be inside an expectable range close to the center of lift. Adding the FTS will also further influence the center of gravity. 
    
    .. image:: /img/aerodynamics/center_of_gravity.JPG
        :align: center
    
    The estimated center of gravity

    
    .. image:: /img/aerodynamics/akku_trench.JPG
        :align: center
    
    The battery-gap (blue)

    For an extensive but simple explanation of the effects of the center of gravity visit https://www.boldmethod.com/learn-to-fly/performance/what-effect-does-center-of-gravity-have-on-your-airplanes-performance/#:~:text=Your%20airplane%20balances%20on%20its,within%20your%20aircraft's%20CG%20limits.

:**Body form**:

    The engine block uses a blended body design. This means that there is no clear cut between wing and body. The engine body is designed in a way that contributes to lift production. There are large, non-lift producing objects, manly the tail, therefore the design is not a flying wing.

    .. image:: /img/hardware/engine_block/engine_block_side.JPG
      :align: center 

Assembly
--------
*~ simcice*

Methods
^^^^^^^

  We connect the individual parts by "welding" the 3d printed parts together. Using a soldering iron, the connecting surfaces are melted together. Any irregularities caused by this method are removed using sandpaper.

  An alternative to this approach is using plastic glue. We use Revels Contact Provisional Glue. Downsides to both methods are the emerging fumes.

  Whilst the engine block, tail and wing mounds are entirely 3d printed, the wings are made using a "skeleton" and foil. This minimizes potential repair times and costs. We do see structural failure of the 3d printed parts as a risk, given the structural integrity and weight of the parts.

Progress
^^^^^^^^

  Complete but unrefined assembly of the engine block and wing mounts

  .. image:: /img/presentation/aether_hawk_real.jpg
    :align: center

Parts
-----
*~ simcice*

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
-----------
*~ teppanf*

- 2x ESP32 with LoRa integrated
- 2x Aikon 30A ESC 2-4S
- 2x T-Motor F1507 3800KV
- 1x Generic 3S LiPo
- 4x Generic Servo

Previous Builds
---------------
*~ simcice*

Concept Trailer
---------------
*~ simcice*

AETHER HAWK started with a concept trailer build and animated in early september 2022. 
The idea behind this design was reusing the old AETHER HEAVY rocket as engine and build 
the aircraft around it. This design was completely modeled and designed in Blender 
(except for the AETHER HEAVY Rocket itself). Even thou this design was purely thought 
to be an inspiration and motivation it already had some aerodynamic decisions implemented 
that ended up being reused in the current design. 

    Watch the trailer here https://www.youtube.com/watch?v=ejGdx6ON9bw
