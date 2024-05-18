# [**Eclipse Paho™ MQTT Python Client**](https://eclipse.dev/paho/files/paho.mqtt.python/html/index.html)   

## 📋 Overview

**The full documentation is available here.

Warning breaking change - Release 2.0 contains a breaking change; see the [**release notes**](https://github.com/eclipse/paho.mqtt.python/releases/tag/v2.0.0) and [**migration details**](
https://eclipse.dev/paho/files/paho.mqtt.python/html/migrations.html) .

This document describes the source code for the Eclipse Paho MQTT Python client library, which implements versions 5.0, 3.1.1, and 3.1 of the MQTT protocol.

This code provides a client class which enables applications to connect to an [**MQTT**](
https://mqtt.org/) broker to publish messages, and to subscribe to topics and receive published messages. It also provides some helper functions to make publishing one off messages to an MQTT server very straightforward.

It supports Python 3.7+.

The MQTT protocol is a machine-to-machine (M2M)/”Internet of Things” connectivity protocol. Designed as an extremely lightweight publish/subscribe messaging transport, it is useful for connections with remote locations where a small code footprint is required and/or network bandwidth is at a premium.

Paho is an Eclipse Foundation project.**



## 📣 Installation Guide

### Supported Platforms

#### paho-mqtt supports the following platforms:

➤ &nbsp; Python 3.7 and higher**


**The latest stable version can be found in the Python Package Index (PyPi) and can be installed using the following command**

```install
pip install paho-mqtt
```



**virtualenv**

```virtualenv
virtualenv paho-mqtt
source paho-mqtt/bin/activate
pip install paho-mqtt
```



**To get the full code (including examples and tests), you can copy the git repository:**

```git repository
git clone https://github.com/eclipse/paho.mqtt.python
```



**Once you have the code, you can also install it from the repository:**

```install repository
cd paho.mqtt.python
pip install -e .
```



**To perform all tests (including MQTT v5 tests), you also need to clone paho.mqtt.testing into the paho.mqtt.python folder:**

```clone
git clone https://github.com/eclipse/paho.mqtt.testing.git
cd paho.mqtt.testing
git checkout a4dc694010217b291ee78ee13a6d1db812f9babd
```


## 🎓 Checking the Installed paho-mqtt Version

**This documentation covers paho-mqtt version 2.0. If you’re working on a system that already has paho-mqtt installed, check the version from your Python prompt like this:**

```mqtt version
import paho.mqtt 

paho.mqtt.__version__
```
