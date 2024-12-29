# Robot-programming
Learning to program robot (DOBOT MG400) with RoboDK and Python.
Wooden planks / Mosaic Tile Placement Automation with RoboDK
Table of Contents

1. Requirements
2. Setup
3. Code Functionality
4. How to Run
5. License

Requirements

- RoboDK: Installed and set up on your machine. Download from https://robodk.com/download.
- RoboDK API: Ensure the Python API for RoboDK is installed. You can install it via pip:
  pip install robodk
- Dobot MG400: The robot used for the task. Ensure it is correctly calibrated and connected to RoboDK.
- Python: Version 3.6 or higher.
- Additional Libraries:
  - math: For mathematical operations like rotations.

Setup

1. Open RoboDK and load a station containing:
   - The Dobot MG400 robot.
   - A reference frame named RefSuctionCup.
   - Targets:
     - Pick 1: The first pick position.
     - Place 1: The first place position.
2. Update the script with the correct names for the robot and reference frame if necessary.

Code Functionality
1. Connecting to RoboDK

The script initializes a connection to the RoboDK API:
```Python
from robodk import robolink, robomath
RDK = robolink.Robolink()
```


2. Robot and Reference Frame

The robot and reference frame are retrieved from the RoboDK station:
```Python

robot = RDK.Item('Dobot MG400', robolink.ITEM_TYPE_ROBOT)
frame = RDK.Item('RefSuctionCup')
```

3. Dynamic Target Generation

* Pick Positions:
  - The script generates multiple pick positions by copying Pick 1 and offsetting them dynamically along the Y-axis.
* Approach Positions:
  - Creates approach positions above the pick positions, offset in the Z-axis for a safe approach.
    

```Python
for i in range(2, num_tiles + 1):
    approach_pick = frame.Paste()
    approach_pick.setName('Pick ' + str(i))
    approach_pick.setPose(approach_pick.Pose() * robomath.transl(ty=-30 * (i - 1)))
```

4. Dynamic Place Targets

Similar logic applies for generating place positions with incremental offsets in the Z-axis and rotations for tile placement.

```Python
approach_pick.setPose(approach_pick.Pose() * robomath.transl(tz=+10 * (i - 1)))
approach_pick.setPose(approach_pick.Pose() * robomath.rotz(math.radians(35 * (i - 1))))
```

5. Pick-and-Place Operations

The robot executes a sequence of movements for each tile:
1. Move to the approach position.
2. Pick the tile using vacuum suction.
3. Place the tile in the designated location.
4. Activate pressure to release the tile.

```Python
for i in range(1, num_tiles + 1):
    robot.MoveJ(approach_pick)
    robot.MoveL(pick)
    robot.setDO('1', 1)  # Vacuum on
    robot.Pause(200)
    robot.MoveJ(approach_pick)
    robot.MoveJ(place1)
    robot.setDO('1', 0)  # Vacuum off
    robot.setDO('2', 1)  # Pressure on
    robot.Pause(200)
    robot.setDO('2', 0)

```

How to Run

1. Ensure RoboDK is running and the station is loaded as described in the Setup section.
2. Save the script as a .py file (e.g., mosaic_tile_automation.py).
3. Run the script in Python:
   python mosaic_tile_automation.py

License

This script is distributed under the MIT License.

Notes

- Adjust wooden planks / num_tiles to change the number of tiles being processed.
- Ensure proper safety measures are followed while operating the robot.

  
![WhatsApp Image 2024-12-29 at 22 17 48_6f76f69c](https://github.com/user-attachments/assets/40d45dab-f14f-4ce9-8f30-389ea01c9c89)


![WhatsApp Image 2024-12-29 at 22 05 30_21e96434](https://github.com/user-attachments/assets/37126ac9-519f-4149-98b8-4798b9b3fd42)
