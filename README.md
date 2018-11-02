_Apologies for my slow responses to issues raised over the last few weeks. I do hope to catch up fully and respond soon. Thanks for all the feedback/suggestions. It's good to know that people are using this._


# homebridge-mqttthing
Homebridge plugin supporting various services over MQTT, originally based on homebrige-mqttswitch and homebridge-mqttlightbulb

   * [Installation](#installation)
   * [Configuration](#configuration)
   * [Supported Accessories](#supported-accessories)
   * [Release notes](#release-notes)

# Installation
Follow the instructions in [homebridge](https://www.npmjs.com/package/homebridge) for the homebridge server installation.
This plugin is published through [NPM](https://www.npmjs.com/package/homebridge-mqttthing) and should be installed "globally" by typing:

    npm install -g homebridge-mqttthing

# Configuration
Configure the plugin in your homebridge config.json file.

Note that setXxx topics are published by Homebridge and should be subscribed-to by devices, and getXxx topics are published by devices to provide feedback on state to Homebridge.

Various different service types are supported by this single 'mqttthing' accessory...

**All values shown below (often within <>) are comments/descriptions, and should not be copied into your configuration file. For an example of an actual configuration file, please see `test/config.json`.**

## Common settings

The following settings apply to all device types:

```javascript
{
    "accessory": "mqttthing",
    "type": "lightbulb",
    "name": "My lightbulb",
    "url": "http://192.168.1.235:1883",
    "username": "MQTT_username",
    "password": "MQTT_password",
    "mqttOptions": { keepalive: 30 },
    "logMqtt": true,
    "topics":
    {
        "getName": 	        "my/get/name/topic"
    },
    "integerValue": true
}
```

### General Settings

`accessory` - must always be set to the value `mqttthing` in order to use **homebridge-mqttthing**

`type` - one of the supported accessory types listed below

`name` - name of accessory, as displayed in HomeKit

`caption` - HomeKit caption/label (optional)

### MQTT Settings

`url` - URL of MQTT server if not localhost port 1883 (optional)

`username` - Username for MQTT server (optional)

`password` - Password for MQTT server (optional)

`mqttOptions` - Object containing all MQTT options passed to https://www.npmjs.com/package/mqtt#client, for MQTT configuration settings not supported above (optional). Any standard settings *not* specified in an **mqttOptions** option will be set by homebridge-mqttthing. Enable MQTT logging with **logMqtt** to check the options provided.

`logMqtt` - Set to true to enable MQTT logging for this accessory (optional, defaults to false)

### MQTT Topics

`getName` - Topic that may be published to send HomeKit the name of the accessory (optional)

### Boolean Value Settings

Homekit Boolean types like on/off use strings "true" and "false" in MQTT messages unless `"integerValue": true` is configured, in which case they use to "1" and "0". Alternatively, specific values can be configured using `onValue` and `offValue` (in which case `integerValue` is ignored). Other Homekit types (integer, string, etc.) are not affected by these settings.

`integerValue` - set to **true** to use the values **1** and **0** to represent Boolean values instead of the strings **"true"** and **"false"** (optional, defaults to false)

`onValue` - configure a specific Boolean true or *on* value (optional)

`offValue` - configure a specific Boolean false or *off* value (optional)

# Supported Accessories

   * [Contact Sensor](#contact-sensor)
   * [Doorbell](#doorbell)
   * [Fan](#fan)
   * [Garage door opener](#garage-door-opener)
   * [Humidity Sensor](#humidity-sensor)
   * [Light bulb](#light-bulb)
   * [Light Sensor](#light-sensor)
   * [Motion Sensor](#motion-sensor)
   * [Occupancy Sensor](#occupancy-sensor)
   * [Outlet](#outlet)
   * [Security System](#security-system)
   * [StatelessProgrammableSwitch](#statelessprogrammableswitch)
   * [Switch](#switch)
   * [Temperature Sensor](#temperature-sensor)

## Contact Sensor

Contact sensor state is exposed as a Boolean. True (or 1 with integer values) maps to `CONTACT_NOT_DETECTED` (sensor triggered)
and False (or 0) maps to `CONTACT_DETECTED` (not triggered). To use different MQTT values, configure `onValue` and `offValue`.

```javascript
{
    "accessory": "mqttthing",
    "type": "contactSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getContactSensorState": "<topic used to provide contact sensor state>"
        "getStatusActive":       "<topic used to provide 'active' status (optional)>",
        "getStatusFault":        "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":     "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":   "<topic used to provide 'low battery' status (optional)>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Doorbell

Doorbell ring switch state can be be `SINGLE_PRESS`, `DOUBLE_PRESS` or `LONG_PRESS`. By default, these events are raised when values of `1`, `2` and `L` respectively are published to the **getSwitch** topic. However, these values may be overridden by specifying an alternative array in the **switchValues** setting.

```javascript
{
    "accessory": "mqttthing",
    "type": "contactSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getSwitch":         "<topic used to provide doorbell switch state>"
        "getBrightness":     "<topic used to get brightness (optional)>",
        "setBrightness":     "<topic used to set brightness (optional)>",
        "getVolume":         "<topic used to get volume (optional)>",
        "setVolume":         "<topic used to set volume (optional)>",
        "getMotionDetected": "<topic used to provide 'motion detected' status (optional, if exposing motion sensor)>"
    },
    "switchValues": "<array of 3 switch values corresponding to single-press, double-press and long-press respectively (optional)>"
}
```

## Fan

Fan running state ('on') is true or false, or 1 or 0 if `integerValue: true` specified.

Fan rotation direction is 0 for clockwise or 1 for anticlockwise.

Fan rotation speed is an integer between 0 (off) and 100 (full speed).

```javascript
{
    "accessory": "mqttthing",
    "type": "fan",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getOn":                "<topic to notify homebridge of 'fan on' status>",
        "setOn":                "<topic published by homebridge to set 'fan on' status>",
        "getRotationDirection": "<topic to notify homebridge of rotation direction (optional)> ",
        "setRotationDirection": "<topic published by homebridge to set rotation direction (optional)>",
        "getRotationSpeed":     "<topic to notify homebridge of rotation speed (optional)",
        "setRotationSpeed":     "<topic published by homebridge to set rotation speed (optional)"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Garage Door Opener

Garage door opener current door state can be **OPEN**, **CLOSED**, **OPENING**, **CLOSING**, **STOPPED**. By default, these use values of `O`, `C`, `o`, `c` and `S` respectively; these defaults can be changed using the **doorValues** setting.

Garage door opener target state can be **OPEN** or **CLOSED**. By default, values of `O` and `C` are used respectively (unless changed through **doorValues**).

Lock current state can be **UNSECURED**, **SECURED**, **JAMMED** or **UNKNOWN**. By default, these use values of `U`, `S`, `J`, `?` respectively; these can be changed using the **lockValues** setting.

Lock target state can be **UNSECURED** or **SECURED**. By default, these use values of `U` and `S` respectively (unless changed through **lockValues**).

```javascript
{
    "accessory": "mqttthing",
    "type": "garageDoorOpener",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "setTargetDoorState":       "test/garage/target",
        "getTargetDoorState":       "test/garage/current",
        "getCurrentDoorState":      "test/garage/current",
        "setLockTargetState":       "test/garagelock/target",
        "getLockTargetState":       "test/garagelock/current",
        "getLockCurrentState":      "test/garagelock/current",
        "getObstructionDetected":   "test/garage/obstruction"
    },
    "doorValues": [ "Open", "Closed", "Opening", "Closing", "Stopped" ],
    "lockValues": [ "Unsecured", "Secured", "Jammed",  "Unknown" ]
}
```

### Topics

`setTargetDoorState` - Topic published when the target door state is changed in HomeKit. Values are _final_ `doorValues` (not opening/closing).

`getTargetDoorState` - Topic that may be published to notify HomeKit that the target door state has been changed externally. Values are _final_ `doorValues` (not opening/closing). May use same topic as `getCurrentDoorState` as above. Omit if all control is through HomeKit.

`getCurrentDoorState` - Topic published to notify HomeKit that a door state has been achieved. HomeKit will expect current door state to end up matching target door state. Values are `doorValues`.

`setLockTargetState` - Topic published when the target lock state is changed in HomeKit. Values are `lockValues`.

`getLockTargetState` - Topic that may be published to notify HomeKit that the target lock state has been changed externally. Values are `lockValues`. May use same topic as `getLockCurrentState` as above. Omit if all control is through HomeKit.

`getLockCurrentState` - Topic published to notify HomeKit that a lock state has been achieved. Values are `lockValues`.

`getObstructionDetected` - Topic published to notify HomeKit whether an obstruction has been detected (Boolean value).

### Values

`doorValues` - Array of 5 door values corresponding to open, closed, opening, closing and stopped respectively. If not specified, defaults to `[ 'O', 'C', 'o', 'c', 'S' ]`.

`lockValues` - Array of 4 lock values corresponding to unsecured, secured, jammed and unknown respectively. if not specified, defaults to `[ 'U', 'S', 'J', '?' ]`.

`integerValue` - Set to true to use values 1 and 0 instead of "true" and "false" respectively for obstruction detected value.


## Humidity Sensor

Current relative humidity must be in the range 0 to 100 percent with no decimal places.

```javascript
{
    "accessory": "mqttthing",
    "type": "humiditySensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getCurrentRelativeHumidity":   "<topic used to provide 'current relative humidity'>",
        "getStatusActive":              "<topic used to provide 'active' status (optional)>",
        "getStatusFault":               "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":            "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":          "<topic used to provide 'low battery' status (optional)>"
    }
}
```


## Light bulb

Light bulb can either use separate topics (for on, brightness, hue and saturation), or it can be configured to use a combined value holding comma-separated hue,sat,val or red,green,blue. 

Hue is 0-360. Saturation is 0-100. Brightness is 0-100. Red, green and blue are 0-255.

If `topics.setHSV` is populated, a combined value is used and any individual brightness, hue and saturation topics are ignored. On/off is sent with `setOn` if configured, or by setting V to 0 when off.

If `topics.setRGB` is populated, a combined value is used in the format red,green,blue (ranging from 0-255). On/off may be sent with `setOn`; brightness, hue and saturation topics are ignored. If `topics.setWhite` is also populated, the white level is extracted and sent separately to the combined RGB value.

If `topics.setRGBW` is populated, a combined value is used in the format red,green,blue,white (ranging from 0-255). On/off may be set with `setOn`; brightness, hue and saturation topics are ignored.

```javascript
{
    "accessory": "mqttthing",
    "type": "lightbulb",
    "name": "<name of lightbulb>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getOn": 	        "<topic to get the status>",
        "setOn": 	        "<topic to set the status>",
        "getBrightness": 	"<topic to get the brightness (optional)>",
        "setBrightness": 	"<topic to set the brightness (optional - if dimmable)>",
        "getHue": 	        "<topic to get the hue (optional)>",
        "setHue": 	        "<topic to set the hue (optional - if coloured)>",
        "getSaturation": 	"<topic to get the saturation (optional)>",
        "setSaturation": 	"<topic to set the saturation (optional - if coloured)>",
        "getHSV":           "<in HSV mode, topic to get comma-separated hue, saturation and value>",
        "setHSV":           "<in HSV mode, topic to set comma-separated hue, saturation and value>",
        "getRGB":           "<in RGB mode, topic to get comma-separated red, green, blue>",
        "setRGB":           "<in RGB mode, topic to set comma-separated red, green, blue>",
        "getRGBW":          "<in RGBW mode, topic to get comma-separated red, green, blue, white>",
        "setRGBW":          "<in RGBW mode, topic to set comma-separated red, green, blue, white>",
        "getWhite":         "<topic to get white level (0-255)> - used with getRGB for RGBW with separately-published white level",
        "setWhite":         "<topic to set white level (0-255)> - used with setRGB for RGBW with separately-published white level",
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>",
    "hex": "true to format combined RGB/RGBW in hexadecimal instead of as comma-separated decimals",
    "hexPrefix": "format combined RGB/RGBW in hexadecimal with specified prefix (typically '#') instead of as comma-separated decimals"
}
```


## Light Sensor

Current ambient light level must be in the range 0.0001 Lux to 100000 Lux to a maximum of 4dp.

```javascript
{
    "accessory": "mqttthing",
    "type": "lightSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getCurrentAmbientLightLevel":  "<topic used to provide 'current ambient light level'>",
        "getStatusActive":              "<topic used to provide 'active' status (optional)>",
        "getStatusFault":               "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":            "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":          "<topic used to provide 'low battery' status (optional)>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Motion Sensor

```javascript
{
    "accessory": "mqttthing",
    "type": "motionSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getMotionDetected":         "<topic used to provide 'motion detected' status>",
        "getStatusActive":           "<topic used to provide 'active' status (optional)>",
        "getStatusFault":            "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":         "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":       "<topic used to provide 'low battery' status (optional)>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Occupancy Sensor

Occupancy sensor state is exposed as a Boolean. True (or 1 with integer values) maps to `OCCUPANCY_DETECTED` (sensor triggered)
and False (or 0) maps to `OCCUPANCY_NOT_DETECTED` (not triggered). To use different MQTT values, configure `onValue` and `offValue`.

```javascript
{
    "accessory": "mqttthing",
    "type": "occupancySensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getOccupancyDetected":      "<topic used to provide 'occupancy detected' status>",
        "getStatusActive":           "<topic used to provide 'active' status (optional)>",
        "getStatusFault":            "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":         "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":       "<topic used to provide 'low battery' status (optional)>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Outlet

An outlet can be configured as a light or as a fan in the Home app.

```javascript
{
    "accessory": "mqttthing",
    "type": "outlet",
    "name": "<name of outlet>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getOn":            "<topic to get the status>",
        "setOn":            "<topic to set the status>",
        "getInUse":         "<topic used to provide 'outlet is in use' feedback>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## Security System

Security System current state can be **STAY_ARM**, **AWAY_ARM**, **NIGHT_ARM**, **DISARMED** or **ALARM_TRIGGERED**. By default, these events are raised when values of `SA`, `AA`, `NA`, `D` and `T` respectively are published to the **getCurrentState** topic. However, these values may be overriden by specifying an alternative array in the **currentStateValues** setting.

Security System target state can be **STAY_ARM**, **AWAY_ARM**, **NIGHT_ARM** or **DISARM**. By default, these states correspond to values of `SA`, `AA`, `NA` and `D`. Homebridge expects to control the target state (causing one of these values to be published to the **setTargetState** topic), and to receive confirmation from the security system that the state has been achieved through a change in the current state (received through the **getCurrentState** topic). The values used for target state can be specified as an an array in the **targetStateValues** setting.

Homebridge publishes a value to the **getCurrentState** topic to indicate the state that the HomeKit users wishes the alarm to be in. The alarm system must echo this state back to the **setCurrentState** topic to confirm that it has set the alarm state appropriately. The alarm system may also publish the ALARM_TRIGGERED value (`T` by default) to the **setCurrentState** topic in order to indicate that the alarm has been triggered. If the alarm system publishes any other states to the **setCurrentState** topic, HomeKit will wait for it to return to the user's target state; in other words, only the HomeKit user can control the arming state of the alarm, not the alarm system itself.

```javascript
{
    "accessory": "mqttthing",
    "type": "securitySystem",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics": {
    "setTargetState": "test/security/target",
    "getTargetState": "test/security/current",
    "getCurrentState": "test/security/current"
    },
    "targetStateValues": [ "StayArm", "AwayArm", "NightArm", "Disarmed" ],
    "currentStateValues": [ "StayArm", "AwayArm", "NightArm", "Disarmed", "Triggered" ]
}
```

### Topics

`setTargetState` - Topic published when the target alarm state is changed in HomeKit. Values are `targetStateValues`.

`getTargetState` - Topic that may be published to notify HomeKit that the target alarm state has been changed externally. Values are `targetStateValues`. May use same topic as `getCurrentState` as above. Omit if all control is through HomeKit.

`getCurrentState` - Topic published to notify HomeKit that an alarm state has been achieved. HomeKit will expect current state to end up matching target state. Values are `currentStateValues`.

### Values

`targetStateValues` - Array of 4 values for target state corresponding to **STAY_ARM**, **AWAY_ARM**, **NIGHT_ARM** and **DISARMED** respectively. If not specified, defaults to `[ 'SA', 'AA', 'NA', 'D' ]`.

`currentStateValues` - Array of 5 values for current state corresponding to **STAY_ARM**, **AWAY_ARM**, **NIGHT_ARM**, **DISARMED** and **ALARM_TRIGGERED** respectively. If not specified, defaults to `[ 'SA', 'AA', 'NA', 'D', 'T' ]`.


## Smoke Sensor

Contact sensor state is exposed as a Boolean. True (or 1 with integer values) maps to `SMOKE_DETECTED` 
and False (or 0) maps to `SMOKE_NOT_DETECTED`. To use different MQTT values, configure `onValue` and `offValue`.

```javascript
{
    "accessory": "mqttthing",
    "type": "smokeSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getSmokeDetected":      "<topic used to provide contact sensor state>"
        "getStatusActive":       "<topic used to provide 'active' status (optional)>",
        "getStatusFault":        "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":     "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":   "<topic used to provide 'low battery' status (optional)>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>"
}
```


## StatelessProgrammableSwitch

Like a doorbell (which is based on it), the state of a stateless programmable switch can be be `SINGLE_PRESS`, `DOUBLE_PRESS` or `LONG_PRESS`. By default, these events are raised when values of `1`, `2` and `L` respectively are published to the **getSwitch** topic. However, these values may be overridden by specifying an alternative array in the **switchValues** setting.

```javascript
{
    "accessory": "mqttthing",
    "type": "statelessProgrammableSwitch",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getSwitch":            "<topic used to provide switch state>"
    },
    "switchValues": "<array of 3 switch values corresponding to single-press, double-press and long-press respectively (optional)>"
}
```


## Switch

On/off switch.

Configuring `turnOffAfter` causes the switch to turn off automatically the specified number of milliseconds after it is turned on.

```javascript
{
    "accessory": "mqttthing",
    "type": "switch",
    "name": "<name of switch>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getOn": 	        "<topic to get the status>",
        "setOn": 	        "<topic to set the status>"
    },
    "integerValue": "true to use 1|0 instead of true|false default onValue and offValue",
    "onValue": "<value representing on (optional)>",
    "offValue": "<value representing off (optional)>",
    "turnOffAfter": <milliseconds after which to turn off automatically>
}
```


## Temperature Sensor

Current temperature must be in the range 0 to 100 degrees Celsius to a maximum of 1dp.

```javascript
{
    "accessory": "mqttthing",
    "type": "temperatureSensor",
    "name": "<name of sensor>",
    "url": "<url of MQTT server (optional)>",
    "username": "<username for MQTT (optional)>",
    "password": "<password for MQTT (optional)>",
    "caption": "<label (optional)>",
    "topics":
    {
        "getCurrentTemperature":        "<topic used to provide 'current temperature'>",
        "getStatusActive":              "<topic used to provide 'active' status (optional)>",
        "getStatusFault":               "<topic used to provide 'fault' status (optional)>",
        "getStatusTampered":            "<topic used to provide 'tampered' status (optional)>",
        "getStatusLowBattery":          "<topic used to provide 'low battery' status (optional)>"
    }
}
```

# Release notes

Version 1.0.16
+ Allow MQTT options to be passed directly, so that any options required can be set (not just those specifically supported by mqttthing)

Version 1.0.15
+ Allowed Garage Door and Security System target states to be modified outside of HomeKit

Version 1.0.14
+ Added `turnOffAfterms` to items with an On characteristic like Switch, causing them to turn off automatically after a specified timeout (in milliseconds).

Version 1.0.13
+ Remove non-ASCII characters from MQTT client ID (thanks, twinkelm)

Version 1.0.12
+ Added Fan

Version 1.0.11
+ Added Light bulb option to publish RGB and RGBW values as hex
+ Added Light bulb option to publish RGB white level separately

Version 1.0.10
+ Allowed separate on/off topic when using combined "hue,saturation,value" topic with Light bulb
+ Added Light bulb combined "red,green,blue" topic support
+ Added Light bulb RGBW support through combined "red,green,blue,white" topic

Version 1.0.9
+ Added option to combine Light bulb hue (0-360), saturation (0-100) and value/brightness (0-100) into a single topic containing "hue,saturation,value"

Version 1.0.8
+ Added Stateless Programmable Switch
+ Added Garage Door Opener

Version 1.0.7
+ Fixed Smoke Sensor

Version 1.0.6
+ Added Temperature Sensor
+ Added Humidity Sensor

Version 1.0.5
+ Added Security System
+ Added Smoke Sensor

Version 1.0.4
+ Fixed Occupancy Sensor values
+ Added Doorbell

Version 1.0.3
+ Added Contact Sensor

Version 1.0.2
+ Added Light Sensor
+ Default sensors to 'active' state

Version 1.0.1
+ Initial public version with Light bulb, Switch, Outlet, Motion Sensor, Occupancy Sensor
