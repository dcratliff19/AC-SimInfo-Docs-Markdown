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
- **`_smVersion`** (`c_wchar * 15`): The version of the shared memory interface.
- **`_acVersion`** (`c_wchar * 15`): The version of Assetto Corsa.
- **`numberOfSessions`** (`c_int32`): The total number of sessions in the simulation.
- **`numCars`** (`c_int32`): The number of cars in the current session.
- **`carModel`** (`c_wchar * 33`): The model name of the player's car (e.g., "Ferrari 488").
- **`track`** (`c_wchar * 33`): The name of the track in use (e.g., "Silverstone").
- **`playerName`** (`c_wchar * 33`): The player's first name.
- **`playerSurname`** (`c_wchar * 33`): The player's surname.
- **`playerNick`** (`c_wchar * 33`): The player's nickname.
- **`sectorCount`** (`c_int32`): The number of sectors on the track.
- **`maxTorque`** (`c_float`): The maximum torque of the car's engine.
- **`maxPower`** (`c_float`): The maximum power of the car's engine, in horsepower.
- **`maxRpm`** (`c_int32`): The maximum revolutions per minute of the car's engine.
- **`maxFuel`** (`c_float`): The maximum fuel capacity of the car, in liters.
- **`suspensionMaxTravel`** (`c_float * 4`): The maximum suspension travel for each of the four wheels.
- **`tyreRadius`** (`c_float * 4`): The radius of each tire.

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
- **`packetId`** (`c_int32`): The ID of the data packet.
- **`gas`** (`c_float`): The current gas (throttle) input as a float (0 to 1).
- **`brake`** (`c_float`): The current brake input as a float (0 to 1).
- **`fuel`** (`c_float`): The amount of fuel left in the car, as a percentage.
- **`gear`** (`c_int32`): The current gear the car is in.
- **`rpms`** (`c_int32`): The current engine RPM.
- **`steerAngle`** (`c_float`): The current steering angle in radians.
- **`speedKmh`** (`c_float`): The current speed of the car in kilometers per hour.
- **`velocity`** (`c_float * 3`): The car's velocity in 3D space (x, y, z).
- **`accG`** (`c_float * 3`): The car's acceleration in 3D space (x, y, z).
- **`wheelSlip`** (`c_float * 4`): The slip of each wheel (front-left, front-right, rear-left, rear-right).
- **`wheelLoad`** (`c_float * 4`): The load on each wheel.
- **`wheelsPressure`** (`c_float * 4`): The pressure of each tire.
- **`wheelAngularSpeed`** (`c_float * 4`): The angular speed of each wheel.
- **`tyreWear`** (`c_float * 4`): The wear level of each tire.
- **`tyreDirtyLevel`** (`c_float * 4`): The dirt level on each tire.
- **`tyreCoreTemperature`** (`c_float * 4`): The core temperature of each tire.
- **`camberRAD`** (`c_float * 4`): The camber angle for each wheel.
- **`suspensionTravel`** (`c_float * 4`): The suspension travel for each wheel.
- **`drs`** (`c_float`): The current drag reduction system (DRS) value (whether active or not).
- **`tc`** (`c_float`): The current traction control level.
- **`heading`** (`c_float`): The car's heading in radians.
- **`pitch`** (`c_float`): The car's pitch angle in radians.
- **`roll`** (`c_float`): The car's roll angle in radians.
- **`cgHeight`** (`c_float`): The height of the car's center of gravity.
- **`carDamage`** (`c_float * 5`): The level of damage to the car (e.g., engine, body, etc.).
- **`numberOfTyresOut`** (`c_int32`): The number of tires that are off the track.
- **`pitLimiterOn`** (`c_int32`): A flag indicating if the pit limiter is engaged.
- **`abs`** (`c_float`): The ABS (anti-lock braking system) level.
---

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
- **`packetId`** (`c_int32`): The ID of the packet containing the data. Typically used to identify different data packets.
- **`status`** (`AC_STATUS`): The status of the simulation, such as whether it's running, paused, or in replay mode.
- **`session`** (`AC_SESSION_TYPE`): The type of session currently active (e.g., practice, race, time attack).
- **`currentTime`** (`c_wchar * 15`): The current lap time as a string, formatted as "mm:ss.xxx".
- **`lastTime`** (`c_wchar * 15`): The player's previous lap time.
- **`bestTime`** (`c_wchar * 15`): The best lap time achieved by the player.
- **`split`** (`c_wchar * 15`): The current split time, formatted similarly to lap times.
- **`completedLaps`** (`c_int32`): The number of laps completed in the session.
- **`position`** (`c_int32`): The player's position in the race or session.
- **`iCurrentTime`**, **`iLastTime`**, **`iBestTime`** (`c_int32`): Integer representations of the respective lap times, typically used for easier comparison and calculation.
- **`sessionTimeLeft`** (`c_float`): The remaining time in the session, in seconds.
- **`distanceTraveled`** (`c_float`): The distance the car has traveled in the session, in meters.
- **`isInPit`** (`c_int32`): A flag indicating whether the car is currently in the pit.
- **`currentSectorIndex`** (`c_int32`): The index of the current sector the car is in (e.g., sector 1, 2, or 3).
- **`lastSectorTime`** (`c_int32`): The time taken to complete the last sector.
- **`numberOfLaps`** (`c_int32`): The total number of laps in the session.
- **`tyreCompound`** (`c_wchar * 33`): The name of the tire compound currently used by the car (e.g., "Soft", "Medium", "Hard").
- **`replayTimeMultiplier`** (`c_float`): The multiplier for replay speed (e.g., 1x for normal speed, 2x for double speed).
- **`normalizedCarPosition`** (`c_float`): The normalized position of the car on the track (a value between 0 and 1).
- **`carCoordinates`** (`c_float * 3`): The 3D coordinates of the car on the track (x, y, z).

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

### SPageFileStatic
Contains static information such as track, car model, and player details.
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

### Accessing sim_info attributes:
1. **`self._acpmf_physics`**: This is a memory-mapped file that corresponds to the physics data in Assetto Corsa. It is mapped using the `mmap.mmap()` function and represents the simulation's physics data in memory.

2. **`self._acpmf_graphics`**: This is a memory-mapped file that holds graphical and session data, such as car position, lap times, and tire compounds.

3. **`self._acpmf_static`**: This memory-mapped file contains static session and car-related data, including track details, car model, and player information.

The `from_buffer()` method is used to map these memory regions into Python structures. Each structure (e.g., `SPageFilePhysics`, `SPageFileGraphic`, and `SPageFileStatic`) defines how the simulation data is organized, and the `from_buffer()` method creates an instance of each structure that allows direct access to the simulation data.

The following code maps these memory regions into Python structures:

```python
self.physics = SPageFilePhysics.from_buffer(self._acpmf_physics)
self.graphics = SPageFileGraphic.from_buffer(self._acpmf_graphics)
self.static = SPageFileStatic.from_buffer(self._acpmf_static)
```

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
