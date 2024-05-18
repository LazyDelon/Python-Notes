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

**This documentation covers paho-mqtt version 2.1.0. If you’re working on a system that already has paho-mqtt installed, check the version from your Python prompt like this:**

```mqtt version
import paho.mqtt 

paho.mqtt.__version__
```


## 🎓 Known limitations

**The following are the known unimplemented MQTT features.**

**When clean_session is False, the session is only stored in memory and not persisted. This means that when the client is restarted (not just reconnected, the object is recreated usually because the program was restarted) the session is lost. This results in a possible message loss.**

**The following part of the client session is lost:**

**➤ &nbsp; QoS 2 messages which have been received from the server, but have not been completely acknowledged.**  
  
> **Since the client will blindly acknowledge any PUBCOMP (last message of a QoS 2 transaction), it won’t hang but will lose this QoS 2 message.**

**➤ &nbsp; QoS 1 and QoS 2 messages which have been sent to the server, but have not been completely acknowledged.**

> **This means that messages passed to publish() may be lost. This could be mitigated by taking care that all messages passed to publish() have a corresponding on_publish() call or use wait_for_publish.**

> **It also means that the broker may have the QoS2 message in the session. Since the client starts with an empty session it don’t know it and will reuse the mid. This is not yet fixed.**

> **Also, when clean_session is True, this library will republish QoS > 0 message across network reconnection. This means that QoS > 0 message won’t be lost. But the standard says that we should discard any message for which the publish packet was sent. Our choice means that we are not compliant with the standard and it’s possible for QoS 2 to be received twice.**

> **You should set clean_session = False if you need the QoS 2 guarantee of only one delivery.**





## 🎓 Usage and API

**Detailed API documentation is available online or could be built from docs/ and samples are available in the examples directory.**

**The package provides two modules, a full Client and few helpers for simple publishing or subscribing.**




## 🎓 Getting Started

**Here is a very simple example that subscribes to the broker $SYS topic tree and prints out the resulting messages:**


```paho-mqtt
import paho.mqtt.client as mqtt

# The callback for when the client receives a CONNACK response from the server.
def on_connect(client, userdata, flags, reason_code, properties):
    print(f"Connected with result code {reason_code}")
    # Subscribing in on_connect() means that if we lose the connection and
    # reconnect then subscriptions will be renewed.
    client.subscribe("$SYS/#")

# The callback for when a PUBLISH message is received from the server.
def on_message(client, userdata, msg):
    print(msg.topic+" "+str(msg.payload))

mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.on_connect = on_connect
mqttc.on_message = on_message

mqttc.connect("mqtt.eclipseprojects.io", 1883, 60)

# Blocking call that processes network traffic, dispatches callbacks and
# handles reconnecting.
# Other loop*() functions are available that give a threaded interface and a
# manual interface.
mqttc.loop_forever()
```

### Client

**You can use the client class as an instance, within a class or by subclassing. The general usage flow is as follows:**

* **Create a client instance**

* **Connect to a broker using one of the connect*() functions**

* **Call one of the loop*() functions to maintain network traffic flow with the broker**

* **Use subscribe() to subscribe to a topic and receive messages**

* **Use publish() to publish messages to the broker**

* **Use disconnect() to disconnect from the broker**

**Callbacks will be called to allow the application to process events as necessary. These callbacks are described below.**



### Network loop

**These functions are the driving force behind the client. If they are not called, incoming network data will not be processed and outgoing network data will not be sent. There are four options for managing the network loop. Three are described here, the fourth in “External event loop support” below. Do not mix the different loop functions.**


#### loop_start() / loop_stop()

```loop_start() / loop_stop()
mqttc.loop_start()

while True:
    temperature = sensor.blocking_read()
    mqttc.publish("paho/temperature", temperature)

mqttc.loop_stop()
```



**These functions implement a threaded interface to the network loop. Calling loop_start() once, before or after connect*(), runs a thread in the background to call loop() automatically. This frees up the main thread for other work that may be blocking. This call also handles reconnecting to the broker. Call loop_stop() to stop the background thread. The loop is also stopped if you call disconnect().**

```loop_forever()
mqttc.loop_forever(retry_first_connection=False)
```



## 🎓 Callbacks

**The interface to interact with paho-mqtt include various callback that are called by the library when some events occur.**

**The callbacks are functions defined in your code, to implement the require action on those events. This could be simply printing received message or much more complex behaviour.**

**Callbacks API is versioned, and the selected version is the CallbackAPIVersion you provided to Client constructor. Currently two version are supported**

* **CallbackAPIVersion.VERSION1: it’s the historical version used in paho-mqtt before version 2.0. It’s the API used before the introduction of CallbackAPIVersion. This version is deprecated and will be removed in paho-mqtt version 3.0.**

* **CallbackAPIVersion.VERSION2: This version is more consistent between protocol MQTT 3.x and MQTT 5.x. It’s also much more usable with MQTT 5.x since reason code and properties are always provided when available. It’s recommended for all user to upgrade to this version. It’s highly recommended for MQTT 5.x user.**

**The following callbacks exists:**

* **on_connect(): called when the CONNACK from the broker is received. The call could be for a refused connection, check the reason_code to see if the connection is successful or rejected.**

* **on_connect_fail(): called by loop_forever() and loop_start() when the TCP connection failed to establish. This callback is not called when using connect() or reconnect() directly. It’s only called following an automatic (re)connection made by loop_start() and loop_forever()**

* **on_disconnect(): called when the connection is closed.**

* **on_message(): called when a MQTT message is received from the broker.**

* **on_publish(): called when an MQTT message was sent to the broker. Depending on QoS level the callback is called at different moment:**

>* **For QoS == 0, it’s called as soon as the message is sent over the network. This could be before the corresponding publish() return.**

>* **For QoS == 1, it’s called when the corresponding PUBACK is received from the broker**

>* **For QoS == 2, it’s called when the corresponding PUBCOMP is received from the broker**

* **on_subscribe(): called when the SUBACK is received from the broker**

* **on_unsubscribe(): called when the UNSUBACK is received from the broker**

* **on_log(): called when the library log a message**

* **on_socket_open, on_socket_close, on_socket_register_write, on_socket_unregister_write: callbacks used for external loop support. See below for details.**

**For the signature of each callback, see the online documentation.**





## 🎓 Subscriber Example

```Subscriber
import paho.mqtt.client as mqtt

def on_subscribe(client, userdata, mid, reason_code_list, properties):
    # Since we subscribed only for a single channel, reason_code_list contains
    # a single entry
    if reason_code_list[0].is_failure:
        print(f"Broker rejected you subscription: {reason_code_list[0]}")
    else:
        print(f"Broker granted the following QoS: {reason_code_list[0].value}")

def on_unsubscribe(client, userdata, mid, reason_code_list, properties):
    # Be careful, the reason_code_list is only present in MQTTv5.
    # In MQTTv3 it will always be empty
    if len(reason_code_list) == 0 or not reason_code_list[0].is_failure:
        print("unsubscribe succeeded (if SUBACK is received in MQTTv3 it success)")
    else:
        print(f"Broker replied with failure: {reason_code_list[0]}")
    client.disconnect()

def on_message(client, userdata, message):
    # userdata is the structure we choose to provide, here it's a list()
    userdata.append(message.payload)
    # We only want to process 10 messages
    if len(userdata) >= 10:
        client.unsubscribe("$SYS/#")

def on_connect(client, userdata, flags, reason_code, properties):
    if reason_code.is_failure:
        print(f"Failed to connect: {reason_code}. loop_forever() will retry connection")
    else:
        # we should always subscribe from on_connect callback to be sure
        # our subscribed is persisted across reconnections.
        client.subscribe("$SYS/#")

mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.on_connect = on_connect
mqttc.on_message = on_message
mqttc.on_subscribe = on_subscribe
mqttc.on_unsubscribe = on_unsubscribe

mqttc.user_data_set([])
mqttc.connect("mqtt.eclipseprojects.io")
mqttc.loop_forever()
print(f"Received the following message: {mqttc.user_data_get()}")
```


## 🎓 Publisher Example

```Publisher
import time
import paho.mqtt.client as mqtt

def on_publish(client, userdata, mid, reason_code, properties):
    # reason_code and properties will only be present in MQTTv5. It's always unset in MQTTv3
    try:
        userdata.remove(mid)
    except KeyError:
        print("on_publish() is called with a mid not present in unacked_publish")
        print("This is due to an unavoidable race-condition:")
        print("* publish() return the mid of the message sent.")
        print("* mid from publish() is added to unacked_publish by the main thread")
        print("* on_publish() is called by the loop_start thread")
        print("While unlikely (because on_publish() will be called after a network round-trip),")
        print(" this is a race-condition that COULD happen")
        print("")
        print("The best solution to avoid race-condition is using the msg_info from publish()")
        print("We could also try using a list of acknowledged mid rather than removing from pending list,")
        print("but remember that mid could be re-used !")

unacked_publish = set()
mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.on_publish = on_publish

mqttc.user_data_set(unacked_publish)
mqttc.connect("mqtt.eclipseprojects.io")
mqttc.loop_start()

# Our application produce some messages
msg_info = mqttc.publish("paho/test/topic", "my message", qos=1)
unacked_publish.add(msg_info.mid)

msg_info2 = mqttc.publish("paho/test/topic", "my message2", qos=1)
unacked_publish.add(msg_info2.mid)

# Wait for all message to be published
while len(unacked_publish):
    time.sleep(0.1)

# Due to race-condition described above, the following way to wait for all publish is safer
msg_info.wait_for_publish()
msg_info2.wait_for_publish()

mqttc.disconnect()
mqttc.loop_stop()
```



### Logger

**The Client emit some log message that could be useful during troubleshooting. The easiest way to enable logs is the call enable_logger(). It’s possible to provide a custom logger or let the default logger being used.**

#### Example:

```
import logging
import paho.mqtt.client as mqtt

logging.basicConfig(level=logging.DEBUG)

mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.enable_logger()

mqttc.connect("mqtt.eclipseprojects.io", 1883, 60)
mqttc.loop_start()

# Do additional action needed, publish, subscribe, ...
[...]
```

**It’s also possible to define a on_log callback that will receive a copy of all log messages.**

#### Example:

```
import paho.mqtt.client as mqtt

def on_log(client, userdata, paho_log_level, messages):
    if paho_log_level == mqtt.LogLevel.MQTT_LOG_ERR:
        print(message)

mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2)
mqttc.on_log = on_log

mqttc.connect("mqtt.eclipseprojects.io", 1883, 60)
mqttc.loop_start()

# Do additional action needed, publish, subscribe, ...
[...]
```



