# Assetto Corsa Shared Memory for Python Applications

This is a Python wrapper for Assetto Corsa's shared memory system, allowing you to access real-time simulation data such as physics, graphics, and static session information. It uses ctypes to map the shared memory from Assetto Corsa into Python structures that can be easily manipulated.

## Requirements

Before using this, ensure that `_ctypes.pyd` is available somewhere in the Python `sys.path`, as Assetto Corsa doesn't include all required Python binaries.

## Installation

1. Copy the third_party directory into your application directory (assettocorsa/apps/python/).
2. Create your application folder in the application directory (assettocorsa/apps/python/)
3. Create your app (example: some_app.py)
4. Use the code example below to import the `_ctypes.pyd` directory in the Python `sys.path`, as Assetto Corsa doesn't include all required Python binaries. 
5. Import `info` from `sim_info` in your application and use it to access the simulation data.

## Folder Structure Example

Here’s an example of how the folder structure should look for the application once it is installed into Assetto Corsa:
```
assettocorsa/apps/python/
├── some_app/
│   └── some_app.py
├── third_party/
│   ├── stdlib/
│   │   └── _ctypes.pyd
│   ├── stdlib64/
│   │   └── _ctypes.pyd
│   ├── sim_info.py

```
### Code Example with Import (some_app.py)

```python
import os
import sys
import platform
try:
    if platform.architecture()[0] == "64bit":
        sysdir = "stdlib64"
    else:
        sysdir = "stdlib"
    sys.path.insert(
        len(sys.path), 'apps/python/third_party')
    os.environ['PATH'] += ";."
    sys.path.insert(len(sys.path), os.path.join(
        'apps/python/third_party', sysdir))
    os.environ['PATH'] += ";."
except Exception as e:
    ac.log("[ACRL] Error importing libraries: %s" % e)

from sim_info import info

print(info.graphics.tyreCompound, info.physics.rpms, info.static.playerNick)
```

## Shared Memory Structures
### `SPageFileStatic` Structure

The `SPageFileStatic` structure contains static session information, such as track details, car model, player information, and other static characteristics of the simulation.

#### Structure Definition:
```python
class SPageFileStatic(ctypes.Structure):
    _pack_ = 4
    _fields_ = [
        ('_smVersion', c_wchar * 15),
        ('_acVersion', c_wchar * 15),
        ('numberOfSessions', c_int32),
        ('numCars', c_int32),
        ('carModel', c_wchar * 33),
        ('track', c_wchar * 33),
        ('playerName', c_wchar * 33),
        ('playerSurname', c_wchar * 33),
        ('playerNick', c_wchar * 33),
        ('sectorCount', c_int32),
        ('maxTorque', c_float),
        ('maxPower', c_float),
        ('maxRpm', c_int32),
        ('maxFuel', c_float),
        ('suspensionMaxTravel', c_float * 4),
        ('tyreRadius', c_float * 4),
    ]
```

#### Field Breakdown:
- **`smVersion[15]`** (`wchar_t`): Version of the Shared Memory structure.
- **`acVersion[15]`** (`wchar_t`): Version of Assetto Corsa.
- **`numberOfSessions`** (`int`): Number of sessions in this instance.
- **`numCars`** (`int`): Max number of possible cars on track.
- **`carModel[33]`** (`wchar_t`): Name of the player’s car.
- **`track[33]`** (`wchar_t`): Name of the track.
- **`playerName[33]`** (`wchar_t`): Name of the player.
- **`playerSurname[33]`** (`wchar_t`): Surname of the player.
- **`playerNick[33]`** (`wchar_t`): Nickname of the player.
- **`sectorCount`** (`int`): Number of track sectors.
- **`maxTorque`** (`float`): Max torque value of the player’s car.
- **`maxPower`** (`float`): Max power value of the player’s car.
- **`maxRpm`** (`int`): Max rpm value of the player’s car.
- **`maxFuel`** (`float`): Max fuel value of the player’s car.
- **`suspensionMaxTravel[4]`** (`float`): Max travel distance of each tyre [Front Left, Front Right, Rear Left, Rear Right].
- **`tyreRadius[4]`** (`float`): Radius of each tyre [Front Left, Front Right, Rear Left, Rear Right].

#### Example Usage:

```python
# Access the car model
print(info.static.carModel)

# Access the track name
print(info.static.track)

# Access player's nickname
print(info.static.playerNick)

# Access maximum engine RPM
print(info.static.maxRpm)

# Access the maximum fuel capacity
print(info.static.maxFuel)
```

---

### `SPageFilePhysics` Structure

The `SPageFilePhysics` structure contains physics-related data, such as vehicle speed, tire wear, engine RPM, and various other dynamic attributes that change throughout the simulation.

#### Structure Definition:
```python
class SPageFilePhysics(ctypes.Structure):
    _pack_ = 4
    _fields_ = [
        ('packetId', c_int32),
        ('gas', c_float),
        ('brake', c_float),
        ('fuel', c_float),
        ('gear', c_int32),
        ('rpms', c_int32),
        ('steerAngle', c_float),
        ('speedKmh', c_float),
        ('velocity', c_float * 3),
        ('accG', c_float * 3),
        ('wheelSlip', c_float * 4),
        ('wheelLoad', c_float * 4),
        ('wheelsPressure', c_float * 4),
        ('wheelAngularSpeed', c_float * 4),
        ('tyreWear', c_float * 4),
        ('tyreDirtyLevel', c_float * 4),
        ('tyreCoreTemperature', c_float * 4),
        ('camberRAD', c_float * 4),
        ('suspensionTravel', c_float * 4),
        ('drs', c_float),
        ('tc', c_float),
        ('heading', c_float),
        ('pitch', c_float),
        ('roll', c_float),
        ('cgHeight', c_float),
        ('carDamage', c_float * 5),
        ('numberOfTyresOut', c_int32),
        ('pitLimiterOn', c_int32),
        ('abs', c_float),
    ]
```

#### Field Breakdown:
- **`packetId`** (`int`): Index of the shared memory’s current step.
- **`gas`** (`float`): Value of gas pedal (0 to 1, fully pressed).
- **`brake`** (`float`): Value of brake pedal (0 to 1, fully pressed).
- **`fuel`** (`float`): Liters of fuel in the car.
- **`gear`** (`int`): Selected gear (0 is reverse, 1 is neutral, 2 is first gear).
- **`rpms`** (`int`): Current engine RPM value.
- **`steerAngle`** (`float`): Angle of steering input in radians.
- **`speedKmh`** (`float`): Current speed of the car in kilometers per hour.
- **`velocity`** (`float * 3`): Car's velocity in 3D space [x, y, z].
- **`accG`** (`float * 3`): Car's acceleration in 3D space [x, y, z].
- **`wheelSlip`** (`float * 4`): Slip speed of each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`wheelLoad`** (`float * 4`): Load on each tire in newtons [Front Left, Front Right, Rear Left, Rear Right].
- **`wheelsPressure`** (`float * 4`): Pressure of each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`wheelAngularSpeed`** (`float * 4`): Angular speed of each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`tyreWear`** (`float * 4`): Current wear level of each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`tyreDirtyLevel`** (`float * 4`): Dirt level on each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`tyreCoreTemperature`** (`float * 4`): Core temperature of each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`camberRAD`** (`float * 4`): Camber angle for each tire in radians [Front Left, Front Right, Rear Left, Rear Right].
- **`suspensionTravel`** (`float * 4`): Suspension travel for each tire [Front Left, Front Right, Rear Left, Rear Right].
- **`drs`** (`float`): Drag Reduction System (DRS) value (0 or 1, enabled or disabled).
- **`tc`** (`float`): Traction Control slip ratio limit (if enabled).
- **`heading`** (`float`): Heading of the car on world coordinates in radians.
- **`pitch`** (`float`): Pitch angle of the car in world coordinates in radians.
- **`roll`** (`float`): Roll angle of the car in world coordinates in radians.
- **`cgHeight`** (`float`): Height of the car's center of gravity.
- **`carDamage`** (`float * 5`): Level of damage for each car section [only the first 4 are valid].
- **`numberOfTyresOut`** (`int`): Number of tires that are off the track.
- **`pitLimiterOn`** (`int`): Indicates if the pit limiter is enabled (0 for false, 1 for true).
- **`abs`** (`float`): Anti-lock braking system (ABS) slip ratio limit (if enabled).

#### Example Usage:

```python
# Access the current engine RPM
print(info.physics.rpms)

# Access the current speed in km/h
print(info.physics.speedKmh)

# Access tire wear for each tire
print(info.physics.tyreWear)

# Access the car's velocity in 3D space (x, y, z)
print(info.physics.velocity)

# Access the current gear
print(info.physics.gear)
```

### `SPageFileGraphic` Structure

The `SPageFileGraphic` structure contains graphical and session-related data, such as the car's position, lap time, tire compound, and other graphical information during the simulation. Below is a breakdown of its fields and how to use them.

#### Structure Definition:
```python
class SPageFileGraphic(ctypes.Structure):
    _pack_ = 4
    _fields_ = [
        ('packetId', c_int32),
        ('status', AC_STATUS),
        ('session', AC_SESSION_TYPE),
        ('currentTime', c_wchar * 15),
        ('lastTime', c_wchar * 15),
        ('bestTime', c_wchar * 15),
        ('split', c_wchar * 15),
        ('completedLaps', c_int32),
        ('position', c_int32),
        ('iCurrentTime', c_int32),
        ('iLastTime', c_int32),
        ('iBestTime', c_int32),
        ('sessionTimeLeft', c_float),
        ('distanceTraveled', c_float),
        ('isInPit', c_int32),
        ('currentSectorIndex', c_int32),
        ('lastSectorTime', c_int32),
        ('numberOfLaps', c_int32),
        ('tyreCompound', c_wchar * 33),
        ('replayTimeMultiplier', c_float),
        ('normalizedCarPosition', c_float),
        ('carCoordinates', c_float * 3),
    ]
```

#### Field Breakdown:
- **`packetId`** (`int`): Index of the shared memory’s current step.
- **`status`** (`AC_STATUS`): Status of the instance. Possible values:
  - `AC_OFF` (0)
  - `AC_REPLAY` (1)
  - `AC_LIVE` (2)
  - `AC_PAUSE` (3)
- **`session`** (`AC_SESSION_TYPE`): Type of session. Possible values:
  - `AC_UNKNOWN` (-1)
  - `AC_PRACTICE` (0)
  - `AC_QUALIFY` (1)
  - `AC_RACE` (2)
  - `AC_HOTLAP` (3)
  - `AC_TIME_ATTACK` (4)
  - `AC_DRIFT` (5)
  - `AC_DRAG` (6)
- **`currentTime`** (`wchar_t[15]`): Current lap time in format "mm:ss.xxx".
- **`lastTime`** (`wchar_t[15]`): Last lap time.
- **`bestTime`** (`wchar_t[15]`): Best lap time.
- **`split`** (`wchar_t[15]`): Time for the current sector.
- **`completedLaps`** (`int`): Number of completed laps by the player.
- **`position`** (`int`): Current player position (standings).
- **`iCurrentTime`** (`int`): Current lap time (integer representation).
- **`iLastTime`** (`int`): Last lap time (integer representation).
- **`iBestTime`** (`int`): Best lap time (integer representation).
- **`sessionTimeLeft`** (`float`): Time left until session closes (in seconds).
- **`distanceTraveled`** (`float`): Distance traveled during the session (in meters).
- **`isInPit`** (`int`): Flag indicating if the player’s car is in the pit (0 for false, 1 for true).
- **`currentSectorIndex`** (`int`): Index of the current sector.
- **`lastSectorTime`** (`int`): Time taken for the last sector.
- **`numberOfLaps`** (`int`): Total number of laps needed to finish the session.
- **`tyreCompound`** (`wchar_t[33]`): The current tire compound used by the car (e.g., "Soft", "Medium", "Hard").
- **`replayTimeMultiplier`** (`float`): Replay speed multiplier (e.g., 1x for normal speed, 2x for double speed).
- **`normalizedCarPosition`** (`float`): Car’s normalized position on the track’s spline (a value between 0 and 1).
- **`carCoordinates`** (`float[3]`): Car position in world coordinates [x, y, z].
- **`penaltyTime`** (`float`): Time penalty for the player (if any).
- **`flag`** (`AC_FLAG_TYPE`): Type of flag being shown. Possible values:
  - `AC_NO_FLAG` (0)
  - `AC_BLUE_FLAG` (1)
  - `AC_YELLOW_FLAG` (2)
  - `AC_BLACK_FLAG` (3)
  - `AC_WHITE_FLAG` (4)
  - `AC_CHECKERED_FLAG` (5)
  - `AC_PENALTY_FLAG` (6)
- **`idealLineOn`** (`int`): Flag indicating if the ideal line is enabled (0 for false, 1 for true).
- **`isInPitLane`** (`int`): Flag indicating if the player’s car is in the pit lane (0 for false, 1 for true).
- **`surfaceGrip`** (`float`): Current grip level of the track surface.
- **`mandatoryPitDone`** (`int`): Flag indicating if the player has completed the mandatory pit (0 for false, 1 for true).
- **`windSpeed`** (`float`): Wind speed during the session.
- **`windDirection`** (`float`): Wind direction in degrees (0-359) during the session.


#### Example Usage:

```python
# Access tire compound (e.g., "Soft", "Medium", "Hard")
print(info.graphics.tyreCompound)

# Access current lap time
print(info.graphics.currentTime)

# Access the player's current position in the race
print(info.graphics.position)

# Access the remaining time in the session
print(info.graphics.sessionTimeLeft)

# Access car's 3D coordinates (x, y, z)
print(info.graphics.carCoordinates)
```
---

### Using the Mapped Data Structures

Once the memory regions are mapped into the Python structures, you can access the simulation data like regular Python objects.

#### Accessing Physics Data:
```python
# Access the RPM of the car
print(self.physics.rpms)

# Access the current speed in km/h
print(self.physics.speedKmh)

# Access tire wear for each tire
print(self.physics.tyreWear)
```
- `self.physics` corresponds to the `SPageFilePhysics` structure, allowing access to various physics-related values, such as `rpms`, `speedKmh`, `tyreWear`, and more.

#### Accessing Graphics Data:
```python
# Access the current tire compound
print(self.graphics.tyreCompound)

# Access the player's position on the track
print(self.graphics.position)

# Access the current lap time
print(self.graphics.currentTime)
```
- `self.graphics` corresponds to the `SPageFileGraphic` structure, allowing you to access graphical and session-related values like tire compound, lap times, car position, etc.

#### Accessing Static Data:
```python
# Access the track name
print(self.static.track)

# Access the car model
print(self.static.carModel)

# Access the player's nickname
print(self.static.playerNick)
```
- `self.static` corresponds to the `SPageFileStatic` structure, which contains static session data such as the track name, car model, player name, and other fixed details.

### Summary of Methods:
- **`self.physics`**: Contains all physics-related data, including car speed, tire wear, engine RPMs, etc.
- **`self.graphics`**: Contains graphical data and session-related information, such as tire compound, lap times, and car position.
- **`self.static`**: Contains static information, such as track name, car model, and player details.

### Example Usage:
Here is an example of how to use these structures in a simple application:

```python
from sim_info import info

# Access and print the player's nickname
print(f"Player's nickname: {info.static.playerNick}")

# Print the current lap time
print(f"Current lap time: {info.graphics.currentTime}")

# Print the current RPM of the car
print(f"Current RPM: {info.physics.rpms}")

# Access tire wear data for all four tires
print(f"Tire wear: {list(info.physics.tyreWear)}")
```
