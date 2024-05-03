.. Copyright (C) 2021 David Miguel Susano Pinto <david.pinto@bioch.ox.ac.uk>

   Permission is granted to copy, distribute and/or modify this
   document under the terms of the GNU Free Documentation License,
   Version 1.3 or any later version published by the Free Software
   Foundation; with no Invariant Sections, no Front-Cover Texts, and
   no Back-Cover Texts.  A copy of the license is included in the
   section entitled "GNU Free Documentation License".

Simulated devices
#################

Devices simulated with Python `microscope <https://www.python-microscope.org>`__ package can be used with Cockpit to test and develop new features without the need for physical devices.
To achieve this the devices must be configured and started in microscope and then added to the cockpit configuration.


Configure and start a simulated camera
-------------------------------------------------

Optinal: to keep all simulated devices code in one place create a separate folder in the microscope code root folder for this, e.g. `simulations`.


Create a python code file called `simulated_camera.py` in the microscope code folder with the following content

.. code-block:: python

    from microscope.device_server import device
    from microscope.simulators import SimulatedCamera

    DEVICES = [
        device(
            SimulatedCamera, 
            host="127.0.0.1", 
            port=8000, 
            conf={"sensor_shape": (512, 512)}
            )
    ]


Start the device server for the configured simulated camera

.. code-block:: bash

    python -m microscope.device_server simulations/simulate_camera.py


Corresponging log messages appear, e.g.: 

.. code-block:: bash

    2024-05-02 15:37:01,959:device-server (__main__):INFO:PID 18915: Device Server started. Press Ctrl+C to exit.
    2024-05-02 15:37:02,064:SimulatedCamera (__mp_main__):INFO:PID 18917: Device initialized; starting daemon.
    2024-05-02 15:37:02,064:SimulatedCamera (__mp_main__):INFO:PID 18917: Serving PYRO:SimulatedCamera@127.0.0.1:8000


Configure Cockpit to access the simulated camera
-------------------------------------------------

Optional: to keep all configurations in one place, create a separate folder for this, e.g. `configs`, in the Cockpit code folder. Make subfolders for `cockpit` and `depot` for the respective configuration files, e.g. `configs/cockpit/`, `configs/depot/'

Create a depot configuration file `configs/depot/depot_simulated_camera.conf` with the following content

.. code-block:: 
    
    [west]
    type: cockpit.devices.microscopeCamera.MicroscopeCamera
    uri: PYRO:SimulatedCamera@127.0.0.1:8000


Create a cockpit configuration file with `configs/cockpit/cockpit_simulated.conf` with the following content

.. code-block::

    [global]
    data-dir: ./data
    depot-files: ./configs/depot/depot_simulated_camera.conf

    [log]
    level: debug
    dir: ./data/logs


Run cockpit with the configuration file

.. code-block:: bash

    cockpit --config-file configs/cockpit/cockpit_simulated_devices.conf
