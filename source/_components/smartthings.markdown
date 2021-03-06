---
layout: page
title: "SmartThings"
description: "Instructions on setting up Samsung SmartThings within Home Assistant."
date: 2018-01-14 00:00
sidebar: true
comments: false
sharing: true
footer: true
featured: true
logo: samsung_smartthings.png
ha_category:
  - Hub
  - Binary Sensor
  - Climate
  - Fan
  - Light
  - Lock
  - Sensor
  - Switch
ha_release: "0.87"
ha_iot_class: "Cloud Push"
redirect_from:
  - /components/smartthings.binary_sensor/
  - /components/binary_sensor.smartthings/
  - /components/smartthings.climate/
  - /components/climate.smartthings/
  - /components/smartthings.fan/
  - /components/fan.smartthings/
  - /components/smartthings.light/
  - /components/light.smartthings/
  - /components/smartthings.lock/
  - /components/lock.smartthings/
  - /components/smartthings.sensor/
  - /components/sensor.smartthings/
  - /components/smartthings.switch/
  - /components/switch.smartthings/
---

Samsung SmartThings is integrated into Home Assistant through the SmartThings Cloud API. The SmartThings component is the main component to integrate all SmartThings related platforms. The basic features of this integration include:

1. Controlling SmartThings devices with pushed state updates from SmartThings.
2. Entities automatically added, removed, or updated when changed in SmartThings (upon Home Assistant restart).
3. Support for multiple SmartThings accounts and locations, each represented as a unique integration in the front-end configuration.
4. No brokers, bridges, or additional dependencies.

See it in action, with a step-by-step setup guide, thanks to a fan! (v0.87 featured):

<div class='videoWrapper'>
<iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/QZHlhQ7fqrA" frameborder="0" allowfullscreen></iframe>
</div>


## {% linkable_title Basic requirements %}

1. A [personal access token](https://account.smartthings.com/tokens) tied to a Samsung or SmartThings account (see below for instructions).
2. Home Assistant setup for [remote access](/docs/configuration/remote/) via a domain name secured with SSL. *Self-signed SSL certificates are not supported by the SmartThings Cloud API.*
3. [`base_url` of the http component](/components/http#base_url) set the URL that Home Assistant is available on the internet.

## {% linkable_title Setup instructions %}

### {% linkable_title Create personal access token %}

1. Log into the [personal access tokens page](https://account.smartthings.com/tokens) and click '[Generate new token](https://account.smartthings.com/tokens/new)'
2. Enter a token name (can be whatever you want), for example, 'Home Assistant' and select the following authorized scopes:
    - Devices (all)
    - Installed Apps (all)
    - Locations (all)
    - Apps (all)
    - Schedules (all)
    - Scenes (all)
3. Click 'Generate token'. When the token is displayed, copy and save it somewhere safe (such as your keystore) as you will not be able to retrieve it again.

### {% linkable_title Configure Home Assistant %}

<p class='note info'>
The SmartThings component is configured exclusively through the front-end. Manual setup through `configuration.yaml` is not available at this time.
</p>

1. From the Home Assistant front-end, navigate to 'Configuration' then 'Integrations'. Under 'Set up a new integration' locate 'SmartThings' and click 'Configure'.
2. Enter the personal access token created above and click 'Submit'
3. When prompted, install the SmartApp:
    1. Open the SmartThings Classic mobile app. Navigate to 'Automation' and select the 'SmartApps' tab.
    2. Click 'Add a SmartApp', scroll to the bottom, and select 'My Apps', then choose 'Home Assistant'.
    3. Optionally change the display name and press 'Done'
    4. Authorize the app by pressing 'Allow'
4. Return to Home Assistant and click 'Submit'.

<p class='note info'>
Advanced: If you have multiple locations in SmartThings, each can be integrated into Home Assistant. Follow the steps above, then for each subsequent location, install the SmartApp and it will automatically add to Home Assistant. This can be completed during step 3 (install SmartApp) above or at any time after that.
</p>

See the [troubleshooting](#troubleshooting) if you are having issues setting up the integration.

## {% linkable_title Events %}

The SmartThings component triggers events for select device capabilities.

### {% linkable_title smartthings.button %}

The component will trigger an event when a device with the [button](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Button) capability is actuated and can be used to trigger automations within Home Assistant. Below is an example of the data payload:

```json
{
  "component_id": "main",
  "device_id": "42a16cf2-fef7-4ee8-b4a6-d32cb65474b7",
  "location_id": "2a54b9fa-f66c-42d9-8488-d8f036b980c8",
  "value": "pushed",
  "name": "Scene Button"
}
```

| Attribute                 | Description
|---------------------------|------------------------------------------------------------------|
`component_id`              | Describes which component of the device triggered the event. `main` represents the parent device. For devices with child-devices, this attribute identifies the child that raised the event.
`device_id`                 | The unique id of the device in SmartThings. This can be located in the HASS device registry or in the [SmartThings Groovy IDE](https://developers.smartthings.com/).
`location_id`               | The unique id of the location the device is part of. This can be found in the config entry registry or in the [SmartThings Groovy IDE](https://developers.smartthings.com/).
`value`                     | Describes the action taken on the button. See the [button](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Button) capability reference for a list of possible values (not all are supported by every device).
`name`                      | The name given to the device in SmartThings.

Event data payloads are logged at the debug level, see [debugging](#debugging) for more information.

## {% linkable_title Platforms %}

SmartThings represents devices as a set of [capabilities](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html) and the SmartThings component maps those to entity platforms in Home Assistant. A single device may be represented by one or more platforms.
- [Binary Sensor](#binary-sensor)
- [Climate](#climate)
- [Fan](#fan) 
- [Light](#light) 
- [Lock](#lock)
- [Sensor](#sensor) 
- [Switch](#switch)

Support for additional platforms will be added in the future.

### {% linkable_title Binary Sensor %}

The SmartThings Binary Sensor platform lets you view devices that have binary sensor-related capabilities. A Binary Sensor entity will be created for each attribute (below) supported by the device.

| Capability        |Attribute     |On-Value
|-------------------|--------------|----------------|
| [`accelerationSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Acceleration-Sensor) | `acceleration` | `active`
| [`contactSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Contact-Sensor)           | `contact`      | `open`
| [`filterStatus`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Filter-Status)             | `filterStatus` | `replace`
| [`motionSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Motion-Sensor)             | `motion`       | `active`
| [`presenceSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Presence-Sensor)         | `presence`     | `present`
| [`tamperAlert`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Tamper-Alert)               | `tamper`       | `detected`
| [`valve`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Valve)                            | `valve`        | `open`
| [`waterSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Water-Sensor)               | `water`        | `wet`

### {% linkable_title Climate %}

The SmartThings Climate platform lets you control devices that have thermostat-related capabilities. For a SmartThings device to be represented by the climate platform, it must have all the capabilities from either "set a" _or_ "set b":

| Capability                          |Climate Features
|-------------------------------------|--------------------------------------------|
| [`thermostat`](https://docs.smartthings.com/en/latest/capabilities-reference.html#thermostat) (set a)                | `operation mode`, `operating state` (state attribute), `target temp high`, `target temp low` and `fan mode`
| [`thermostatMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Mode) (set b)            | `operation mode`
| [`thermostatCoolingSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Cooling-Setpoint) (seb b) | `target temp low`
| [`thermostatHeatingSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Heating-Setpoint) (set b) | `target temp high`
| [`temperatureMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Temperature-Measurement) (set b)    |
| [`thermostatOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Operating-State)          | `operating state` (state attribute)
| [`thermostatFanMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Fan-Mode)                 | `fan mode`
| [`relativeHumidityMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Relative-Humidity-Measurement)       | `humidity` (state attribute)

### {% linkable_title Fan %}

The SmartThings Fan platform lets you control devices that have fan-related capabilities. For a SmartThings device to be represented by the fan platform, it must have one or more of the capabilities below in addition to the [`switch`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Switch) capability.

| Capability        |Fan Features
|-------------------|------------------------------------------------------------|
| [`fanSpeed`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Fan-Speed)            | `speed` (`off`, `low`, `medium`, and `high`)

### {% linkable_title Light %}

The SmartThings Light platform lets you control devices that have light-related capabilities. For a SmartThings device to be represented by the light platform, it must have one or more of the capabilities below in addition to the [`switch`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Switch) capability.

| Capability        |Light Features
|-------------------|------------------------------------------------------------|
| [`switchLevel`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Switch-Level)            | `brightness` and `transition`
| [`colorControl`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Color-Control)            | `color`
| [`colorTemperature`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Color-Temperature)            | `color_temp`

### {% linkable_title Lock %}

The SmartThings Lock platform lets you control devices that have the [`lock`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Lock) capability, showing current lock status and supporting lock and unlock commands.

### {% linkable_title Sensor %}

The SmartThings Sensor platform lets your view devices that have sensor-related capabilities. A Sensor entity is created for each attribute (below) supported by the device.

| Capability        |Attributes     |
|-------------------|---------------|
| [`activityLightingMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Activity-Lighting-Mode)            | `lightingMode`
| [`airConditionerMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Air-Conditioner-Mode)                | `airConditionerMode`
| [`airQualitySensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Air-Quality-Sensory)                   | `airQuality`
| [`alarm`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Alarm)                                            | `alarm`
| [`audioVolume`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Audio-Volume)                               | `volume`
| [`battery`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Battery)                                        | `battery`
| [`bodyMassIndexMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Body-Mass-Index-Measurement)   | `bmiMeasurement`
| [`bodyWeightMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Body-Weight-Measurement)          | `bodyWeightMeasurement`
| [`carbonDioxideMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Carbon-Dioxide-Measurement)    | `carbonDioxide`
| [`carbonMonoxideDetector`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Carbon-Monoxide-Detector)        | `carbonMonoxide`
| [`carbonMonoxideMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Carbon-Monoxide-Measurement)  | `carbonMonoxideLevel`
| [`dishwasherOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Dishwasher-Operating-State)    | `machineState`, `dishwasherJobState` and `completionTime`
| [`doorControl`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Door-Control)                               | `door`
| [`dryerMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Dryer-Mode)                                   | `dryerMode`
| [`dryerOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Dryer-Operating-State)              | `machineState`, `dryerJobState` and `completionTime`
| [`dustSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Dust-Sensor)                                 | `fineDustLevel` and `dustLevel`
| [`energyMeter`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Energy-Meter)                               | `energy`
| [`equivalentCarbonDioxideMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Equivalent-Carbon-Dioxide-Measurement) | `equivalentCarbonDioxideMeasurement`
| [`formaldehydeMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Formaldehyde-Measurement)       | `formaldehydeLevel`
| [`garageDoorControl`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Garage-Door-Control)                  | `door`
| [`illuminanceMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Illuminance-Measurement)         | `illuminance`
| [`infraredLevel`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Infrared-Level)                           | `infraredLevel`
| [`lock`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Lock)                                              | `lock`
| [`mediaInputSource`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Media-Input-Source)                    | `inputSource`
| [`mediaPlaybackRepeat`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Media-Playback-Repeat)              | `playbackRepeatMode`
| [`mediaPlaybackShuffle`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Media-Playback-Shuffle)            | `playbackShuffle`
| [`mediaPlayback`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Media-Playback)                           | `playbackStatus`
| [`odorSensor`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Odor-Sensor)                                 | `odorLevel`
| [`ovenMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Oven-Mode)                                     | `ovenMode`
| [`ovenOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Oven-Operating-State)                | `machineState`, `ovenJobState` and `completionTime`
| [`ovenSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Oven-Setpoint)                             | `ovenSetpoint`
| [`powerMeter`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Power-Meter)                                 | `power`
| [`powerSource`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Power-Source)                               | `powerSource`
| [`refrigerationSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Refrigeration-Setpoint)           | `refrigerationSetpoint`
| [`relativeHumidityMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Relative-Humidity-Measurement) | `humidity`
| [`robotCleanerCleaningMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Robot-Cleaner-CleaningMode)    | `robotCleanerCleaningMode`
| [`robotCleanerMovement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Robot-Cleaner-Movement)            | `robotCleanerMovement`
| [`robotCleanerTurboMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Robot-Cleaner-Turbo-Mode)         | `robotCleanerTurboMode`
| [`signalStrength`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Signal-Strength)                         | `lqi` and `rssi`
| [`smokeDetector`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Smoke-Detector)                           | `smoke`
| [`temperatureMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Temperature-Measurement)         | `temperature`
| [`thermostatCoolingSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Cooling-Setpoint)  | `coolingSetpoint`
| [`thermostatFanMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Fan-Mode)                  | `thermostatFanMode`
| [`thermostatHeatingSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Heating-Setpoint)  | `heatingSetpoint`
| [`thermostatMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Mode)                         | `thermostatMode`
| [`thermostatOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Operating-State)    | `thermostatOperatingState`
| [`thermostatSetpoint`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Thermostat-Setpoint)                 | `thermostatSetpoint`
| [`tvChannel`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Tv-Channel)                                   | `tvChannel`
| [`tvocMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Tvoc-Measurement)                       | `tvocLevel`
| [`ultravioletIndex`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Ultraviolet-Index)                     | `ultravioletIndex`
| [`voltageMeasurement`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Voltage-Measurement)                 | `voltage`
| [`washerMode`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Washer-Mode)                                 | `washerMode`
| [`washerOperatingState`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Washer-Operating-State)            | `machineState`, `washerJobState` and `completionTime`
| [`windowShade`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Window-Shade)                               | `windowShade`

### {% linkable_title Switch %}

The SmartThings Switch platform lets you control devices that have the [`switch`](https://smartthings.developer.samsung.com/develop/api-ref/capabilities.html#Switch) capability that are not already represented by a more specific platform.

## {% linkable_title Troubleshooting %}

### {% linkable_title Setup %}

Perform the following steps if you receive one of the following error messages while attempting to setup the integration:

- "SmartThings could not validate the endpoint configured in base_url. Please review the component requirements."
- "Unable to setup the SmartApp. Please try again."

#### {% linkable_title Checklist %}

1. Ensure `base_url` is properly set to the _external address_ that Home Assistant is available to the internet. SmartThings must be able to reach this address.
1. Validate there are no problems with your certificate or SSL configuration by using an online checker, such as [https://www.digicert.com/help/](https://www.digicert.com/help/).
1. Some reverse proxy configuration settings can interfere with communication from SmartThings.  For example, TLSv1.3 is not supported.  Setting the supported cipher suite too restrictly will prevent handshaking. The following NGINX SSL configuration is known to work:
   ```nginx
   # cert.crt also contains intermediate certificates
   ssl_certificate /path/to/cert.crt;
   ssl_certificate_key /path/to/cert.key;
   ssl_dhparam /path/to/dhparam.pem;
   ssl_protocols TLSv1.2;
   ssl_ciphers 'EECDH+AESGCM:EDH+AESGCM:AES256+EECDH:AES256+EDH';
   ssl_prefer_server_ciphers on;
   ssl_ecdh_curve secp384r1;
   ssl_session_timeout  10m;
   ssl_session_cache shared:SSL:10m;
   ssl_session_tickets off;
   ```
1. While the error message (above) is being displayed, run the following command from outside your local network to confirm it is responding to the ping lifecycle event:
    ```bash
    curl -X POST https://{BASE_URL}/api/webhook/{WEBHOOK_ID} -H "Content-Type: application/json; charset=utf-8" -d $'{"lifecycle": "PING", "executionId": "00000000-0000-0000-0000-000000000000", "locale": "en", "version": "1.0.0", "pingData": { "challenge": "00000000-0000-0000-0000-000000000000"}}'
    ```
    Where `{BASE_URL}` is your external address and `{WEBHOOK_ID}` is the value of `webhook_id` from `.storage/smartthings` in your Home Assistant configuration directory.

    The expected response is:
    ```bash
    {"pingData": {"challenge": "00000000-0000-0000-0000-000000000000"}}
    ```

If you have completed the checklist above and are still unable to setup the platform, [activate debug logging](#debugging) for the SmartThings component and include the log messages up until the point of failure in [a new issue](https://github.com/home-assistant/home-assistant/issues).

### {% linkable_title Debugging %}

The SmartThings component will log additional information about push updates received, events fired, and other messages when the log level is set to `debug`. Add the the relevent line below to the `configuration.yaml`:

```yaml
logger:
  default: info
  logs:
    homeassistant.components.smartthings: debug
```
