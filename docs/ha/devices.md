# Devices

## Shelly Plug S

[Shelly Plug S](https://templates.blakadder.com/shelly_plug_S.html) can be used
with the [Tasmota firmware](https://tasmota.github.io/docs/).

Install the firmware:

```
http://shellyip/ota?url=http://ota.tasmota.com/tasmota/shelly/mg2tasmota-ShellyPlugS.zip
```

Connect to Tasmota Wifi SSID and configure the plug. After that open the URL
`http://shellyip/`.

Configure template:

```
{"NAME":"Shelly Plug S","GPIO":[57,255,56,255,0,134,0,0,131,17,132,21,0],"FLAG":2,"BASE":45}
```

Configure device:

```
BlitzWolf SHP (45)
```

Calibrate the device (with readings of 235V and 35W):

```
savedata 1
VoltageSet 235
PowerSet 35.0
savedata 0
```

The information can be found in this
[video](https://youtu.be/EjJSIUAlPxs).
