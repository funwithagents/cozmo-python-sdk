# (Forked version) Cozmo - Python SDK

![Forked Cozmo Python SDK](docs/source/images/cozmo-sdk-img.jpg)

This repository is a fork of [Anki Cozmo - Python SDK](https://github.com/anki/cozmo-python-sdk) for compatibility with recent Python versions (3.12+) and latest version of Cozmo application ([iOS](https://apps.apple.com/fr/app/cozmo/id1154282030) or [Android](https://play.google.com/store/apps/details?id=com.digitaldreamlabs.cozmo2)).

## Documentation

You can access the SDK documentation here: https://funwithagents-cozmo-python-sdk.readthedocs.io/
(which is directly built from this repository, but without any changes from the original documentation, so it corresponds to the original SDK documentation)

> [!NOTE]
> This was needed as Anki website is down and thus their documentation is not available anymore.

## Installation

Based on your OS, check the corresponding **"Installation - XXX"** page in the documentation.

As this forked version of the SDK in not available as a Pypi package for pip installation, so you will need to call:

```sh
$ pip install git+https://github.com/funwithagents/cozmo-python-sdk.git
```
instead of `pip install 'cozmo[camera]'` as it is written in the (original) SDK documentation


## Architecture & link with Mobile app

Cozmo SDK works by communicating with the Cozmo application ([iOS](https://apps.apple.com/fr/app/cozmo/id1154282030) or [Android](https://play.google.com/store/apps/details?id=com.digitaldreamlabs.cozmo2)) installed on a mobile phone, connected via USB to the computer running the SDK.

The Cozmo application then handles the calls to the APIs with the robot. This enables to have access in the SDK to:
- low-level APIs of the robot (onboard API)
- but also high level APIs running in the app (offboard API)

You can check the **"Mobile Device Setup"** section of the installation page corresponding to your OS for more information on how to connect your mobile phone.

> [!IMPORTANT]
> For the moment, we have only validated the usage of the Cozmo SDK using an Android phone and ADB.
> The usage with an iOS phone still needs to be validated/updated.

## SDK & Application compatibility workaround

The latest version of the Cozmo mobile application (3.6.X) seems to rely on a newer version (3.6) of `cozmoclad` library, which is different from the latest version (3.4) that is currently available on Pypi.
As the `cozmoclad` library cannot be found as an open source library, and it seems that some files and versions in it are directly generated from .clad files that are not available.

By default, this leads to errors when trying to connect to the robot (via the app) from the SDK, because of some version checks, which prevent from connecting to the robot:
> ERROR CLAD version mismatch (to_game) da3e90a6724adf4b5e7cf5764e960096 != 52ab2f222c9ba0e9c5590901121198ec

HOWEVER, even though the version has changed, the protocol seems to have remained the same between the two versions (3.4 and 3.6). So, as a workaround, we have added a function to be able to bypass the version check and allow connection with the robot (without any issue caused by this version mismatch):
```Python
cozmo.conn.bypass_version_check(True)
```

So the usage of the SDK becomes the following:
```Python
import cozmo

def cozmo_program(robot: cozmo.robot.Robot):
    robot.say_text("Hello World").wait_for_completed()

cozmo.conn.bypass_version_check(True)
cozmo.run_program(cozmo_program)
```
