# Eva Python SDK

[![PyPI version](https://badge.fury.io/py/evasdk.svg)](https://badge.fury.io/py/evasdk) ![Build status](https://github.com/automata-tech/eva_python_sdk/workflows/Build%20and%20test/badge.svg) [![codecov](https://codecov.io/gh/automata-tech/eva_python_sdk/branch/development/graph/badge.svg)](https://codecov.io/gh/automata-tech/eva_python_sdk)

The Eva Python SDK provides convenient access to the Automata Eva API from applications written in Python 3.

__* This SDK is currently in beta__

- [Eva Python SDK](#eva-python-sdk)
  - [Installation](#installation)
    - [Pip](#pip)
    - [Pipenv](#pipenv)
    - [Detail Instructions](#detail-instructions)
  - [Examples](#examples)
    - [Eva](#eva)
    - [evasdk.eva_http and evasdk.eva_ws](#evasdkeva_http-and-evasdkeva_ws)
  - [Versioning](#versioning)
  - [Logging](#logging)
  - [Bugs and feature requests](#bugs-and-feature-requests)
  - [Testing](#testing)
  - [Documentation](#documentation)
  - [License](#license)

## Installation

__Requires Python 3, not compatible with Python 2__

We support Python `3.6` and later.

### Pip

Make sure you have Python3 and pip installed, then run the following command:

```bash
$ pip install evasdk

# Or for a specific version, i.e the lastest compatible version 2.x.x:
$ pip install evasdk~=2.0.0
```

### Pipenv

Make sure you have Python3 and Pipenv installed, then run the following command:

```bash
$ pipenv install evasdk

# Or for a specific version, i.e the lastest compatible version 2.x.x:
$ pipenv install evasdk~=2.0.0
```

### Detail Instructions

If your not familiar with Python or for more detailed instructions please refer to our wiki:

- [Windows installation instructions](https://github.com/automata-tech/eva_python_sdk/wiki/Windows-Installation)
- [Mac installation instructions](https://github.com/automata-tech/eva_python_sdk/wiki/Mac-Installation)

## Examples

The following section provides some getting started example code, for more detailed examples please go to our [eva_examples repository](https://github.com/automata-tech/eva_examples).

### Eva

The Eva object allows you to directly control an Eva robot. It provides lots of useful helper function for interacting with the robot.

**Connecting**

```python
host = '<your_eva_IP_here>'
token = '<your_token_here>'

eva = Eva(host, token)
```

**GoTo movement**

```python
eva = Eva(host_ip, token)

with eva.lock():
    eva.control_wait_for_ready()
    eva.control_go_to([0, 0, 0, 0, 0, 0], mode='teach')
```

**Toolpath create and run**

```python
toolpath = {
    "metadata": {
        "version": 2,
        "default_max_speed": 0.25,
        "payload": 0,
        "analog_modes": {
            "i0": "voltage",
            "i1": "voltage",
            "o0": "voltage",
            "o1": "voltage"
        },
        "next_label_id": 3
    },
    "waypoints": [{
        "label_id": 1,
        "joints": [0, 0.5235987755982988, -1.7453292519943295, 0, -1.9198621771937625, 0]
    }, {
        "label_id": 2,
        "joints": [0.18392622441053394, 0.8259819316864014, -2.050006151199341, 0.1785774528980255, -1.6037521743774412, -0.549331545829773]
    }],
    "timeline": [{
        "type": "home",
        "waypoint_id": 0
    }, {
        "type": "trajectory",
        "trajectory": "joint_space",
        "waypoint_id": 1
    }, {
        "type": "trajectory",
        "trajectory": "joint_space",
        "waypoint_id": 0
    }]
}

eva = Eva(host, token)

with eva.lock():
    eva.control_wait_for_ready()
    eva.toolpaths_use(toolpath)
    eva.control_home()
    eva.control_run(loop=1, mode='teach')
```

Please refer to the examples directory for more SDK usage examples.

### evasdk.eva_http and evasdk.eva_ws

These can be used to interact directly with the HTTP and Websocket APIs. Useful when you don't want the managed websocket connection provided by the evasdk.Eva object.

## Versioning

To determine which version of the SDK works with your Eva's software version number (found on the Choreograph config page), please use the following chart:

| SDK Version   | Supported Eva Version |
| ------------- | --------------------- |
| 1.0.0         | 2.0.0 - 2.1.2         |
| 2.0.0         | 3.0.0 - 3.0.1         |
| 3.0.0+        | 3.1.0                 |

For more information on how to install a particular version of the SDK, please refer to the [Installation](#Installation) section. We use the [Semver](https://semver.org/) version numbering stratergy.

## Logging

The SDK uses Debug and Error level logging exclusively. Each Eva instance will log using the name `evasdk.Eva:<host_name_here>`. If you wish to enable the debug logging:

```python
logging.basicConfig(level=logging.DEBUG)
```

## Bugs and feature requests

Please raise any bugs or feature requests as a Github issues. We also gratefully accept pull requests for features and bug fixes.

## Testing

```bash
# to run all test files in tests directory:
$ pipenv run test

# or to run a single test file:
$ pipenv shell
$ pipenv run testd tests/<test-name>_test.py

# some test require supplying ip and token via the `--ip` and `--token` arguements:
$ pipenv run test --ip 172.16.16.2 --token abc-123-def-456

# Tests requiring the robot or long amounts of time will not run by default,
# these require flags to be enabled, a full list of flags is availble with the help flag:
$ pipenv run test -h
```

## Documentation

Find the documentation [here](https://eva-python-sdk.readthedocs.io/en/latest/).

We are using [Sphinx](https://github.com/sphinx-doc/sphinx) to generate documentation, and the documentation is deployed with [Read The Docs](https://github.com/readthedocs/readthedocs.org).

We have added a few extensions to Sphinx
-   **sphinx.ext.autodoc** to automatically generate package documentation based off of Python docstrings.
-   **m2r2** to convert rST to MD, this is so we can include `README.md` to the online docs.

When adding new files in `evasdk/`, it's important to remember to update the documentation by running the following:
```bash
cd docs
sphinx-apidoc -f  -o . ..
```
We need to pass the force flag `-f` to overwrite the modules.rst file as otherwise Sphinx will skip over searching the `evasdk` directory.

To generate a local version of the documentation:
```bash
cd docs
make html
```

Then open the file `docs/_build/html/index.html` in your browser.

## License

This code is free to use under the terms of the Apache 2 license. Please refer to LICENSE for more information.
