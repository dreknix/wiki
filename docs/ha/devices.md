---
tags:
  - HomeAssistant
---

# Devices

## Shelly Plug S


[Shelly Plug S](https://templates.blakadder.com/shelly_plug_S.html) can be used
with the [Tasmota firmware](https://tasmota.github.io/docs/).

Connect to Wifi SSID of Shelly Plug S and connect to web interface
[http://192.168.33.1/](http://192.168.33.1) and configure local wifi network.

Install the firmware:

```
http://shellyip/ota?url=http://ota.tasmota.com/tasmota/shelly/mg2tasmota-ShellyPlugS.zip
```

Connect to Tasmota Wifi SSID and configure the plug. After that open the URL
`http://shellyip/`.

Configure template (`Configure` > `Configure Other`):

```
{"NAME":"Shelly Plug S","GPIO":[57,255,56,255,0,134,0,0,131,17,132,21,0],"FLAG":2,"BASE":45}
```

Configure device (`Configure` > `Configure Module`):

```
BlitzWolf SHP (45)
```

Configure MQTT (`Configure` > `Configure MQTT`:

* Host: //IP address of Home Assistant//
* User: //user in Home Assistant//
* Password: //user in Home Assistant//

Calibrate the device (with readings of 235V and 35W):

```
savedata 1
VoltageSet 235
PowerSet 35.0
savedata 0
```

The information can be found in this
[video](https://youtu.be/EjJSIUAlPxs).
