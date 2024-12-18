# Assetto Corsa Shared Memory for Python Applications

This is a Python wrapper for Assetto Corsa's shared memory system, allowing you to access real-time simulation data such as physics, graphics, and static session information. It uses ctypes to map the shared memory from Assetto Corsa into Python structures that can be easily manipulated.

## Requirements

Before using this, ensure that `_ctypes.pyd` is available somewhere in the Python `sys.path`, as Assetto Corsa doesn't include all required Python binaries.

## Folder Structure Example

Here’s an example of how the folder structure should look for the application:
some_app
    DLLs
        _ctypes.pyd
    some_app.py


### Code Example (some_app.py)

```python
import os
import sys
sys.path.insert(0, os.path.join(os.path.dirname(__file__), 'DLLs'))

from sim_info import info

print(info.graphics.tyreCompound, info.physics.rpms, info.static.playerNick)
```

## sim_info Module

The `sim_info` module contains the shared memory structures that will allow you to access the data.

## Installation

1. Copy the `sim_info.py` code into your application directory.
2. Make sure the `_ctypes.pyd` file is placed in the `DLLs` folder as shown above.
3. Import `info` from `sim_info` in your application and use it to access the simulation data.

## Shared Memory Structures

### SPageFilePhysics

Contains physics-related data like vehicle speed, tire wear, engine RPM, and more.


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

### Usage
```python
from sim_info import info

# Access simulation data
print(info.graphics.tyreCompound)  # Current tire compound
print(info.physics.rpms)  # Engine RPM
print(info.static.playerNick)  # Player's nickname
```

```
