<div align="center">
  <img src="banner.jpg" alt="Software Banner" width="100%">
  <p>☕ <strong>Support the project:</strong> <a href="https://buymeacoffee.com/bmccruz">Buy Me a Coffee</a></p>
</div>

# homebridge-philipstv-hue
Homebridge module for Philips TV (with JointSpace enabled) with Power on/off, Sound, Ambilight with Hue On/Off and source input control

## License

This project is licensed under the ISC License. See [LICENSE](LICENSE) for details.

## Description

This plugin is a fork of [gw-wiscon](https://github.com/gw-wiscon)'s
[homebridge-philipstv-x](https://www.npmjs.com/package/homebridge-philipstv-x)
with additional support for Sound control, Ambilight brightness control and
input selection control by [jebabin](https://github.com/jebabin).
It has been modified to work on a 43PUS6753 TV (2018 model without Android)
and may not work on older one. Code may need ajustement for Ambilight to work
on other 2018 models.

This was further modified by [Arkku](https://github.com/arkku) to use the
HomeKit Television service, which allows remote control from the Control Center
widget (including volume control by pressing the physical volume up/down
buttons). Other modifications include ChromeCast-based power-on option, and
support for an external "etherwake" executable.

Modified by [bmccruz](https://github.com/bmccruz) to add support to Ambilight+Hue for Saphi TVs.

## Features

- Control TV power, volume and mute through HomeKit's Television service.
- Use HomeKit's Control Center remote, including configurable Info and Play/Pause buttons.
- Turn Ambilight on or off and adjust its brightness. Enable Hue lamp power control when the TV supports Ambilight+Hue.
- Change input sources with the HomeKit input selector. This sends a sequence  of remote-control buttons, so behavior may vary by TV; the selector can be hidden in the configuration.
- Wake the TV with Wake-on-LAN, an optional `etherwake` executable, or Chromecast where supported.
- Optionally poll the TV to keep power, audio and enabled lighting states synchronized with HomeKit.
- Connect over HTTP or HTTPS and use digest credentials for authenticated JointSpace APIs.

Feature availability depends on the TV model, firmware, JointSpace API version
and configuration. This plugin has been tested on a 2018 Saphi TV; other models
may require different settings or may not support every feature.

## Installation

1. Install homebridge using: `npm install -g homebridge`
2. Install this plugin using: `npm install -g homebridge-philipstv-hue`
3. Update your configuration file. See the sample below.

If you do not wish to install from npm (e.g, because you are using a fork), you
may also clone the git repository into some directory like
`/usr/local/homebridge/plugins/homebridge-philipstv-hue` and then specify the
plugin directory as an option (`-P /usr/local/homebridge/plugins`, typically
using `HOMEBRIDGE_OPTS` in `/etc/default/homebridge`).

## Configuration
 
Example accessory config (needs to be added to the Homebridge `config.json`):

  ```
 "accessories": [
     {
        "accessory": "PhilipsTV",
        "name": "TV",
        "ip_address": "10.0.1.23",
        "poll_status_interval": "60",
        "model_year": 2018,
        "has_ssl": true,
        "has_ambilight": true,
        "has_hue": true,
        "has_chromecast": true,
        "hide_input_selector": false,
        "info_button": "Source",
        "playpause_button": "Options",
        "wol_url": "wol://18:8e:d5:a2:8c:66"
     }
 ]
  ```
 
To be able to power on the TV when the TV is in standby mode, you will need the `wol_url` 
parameter with the MAC address of your TV. You may also specify
`etherwake_exec` with a path to the `etherwake` executable (takes the MAC
address as argument). In some cases this seems to work using
`/usr/sbin/ethewrake` when the built-in WoL fails, so try both before giving
up.

 ```
"accessories": [
    {
        "accessory": "PhilipsTV",
        "name": "TV",
        "ip_address": "10.0.1.23",
        "poll_status_interval": "60",
        "model_year" : "2018",
        "has_ssl": false,
        "wol_url": "wol://18:8e:d5:a2:8c:66",
        "etherwake_exec": "/usr/sbin/etherwake"
    }
]
 ```

For TVs with a built-in ChromeCast, you may also have better success waking it
up through that. This is supported with the option `"has_chromecast": true`, in
which case the ChromeCast URL is accessed before trying using the typical power
state method. You may need to enable ChromeCast power-up option in the TV settings
menu (this will increase standby power consumption, so try first if your TV
wakes up consistently without it).

The Control Center widget info button is mapped to the options menu by default,
but may be configured to any other remote button (e.g., `Home` or `Source`)
with the option `"info_button": "Options"`. Likewise the play/pause button may
be remapped, with `"playpause_button": "PlayPause"`. The somewhat flaky input
source selector (which simply sends a sequence of buttons, hoping to change the
input) may be disabled with `"hide_input_selector": true`.

## Credentials for 2016 and newer Android TV models

Philips Android TVs from 2016 onward use an authenticated HTTPS
[JointSpace](http://jointspace.sourceforge.net/) API version 6. Control and
status requests require a pre-generated username and password for
[digest authentication](https://en.wikipedia.org/wiki/Digest_access_authentication).
Pair the TV once using the
[philips\_android\_tv](https://github.com/suborb/philips_android_tv) script.

Here is an example pairing call for philips\_android\_tv :
```
python ./philips.py --host 10.0.1.23 pair
```

You can then add username and password key in your homebridge config, example:
```
"accessories": [
  {
  	"accessory": "PhilipsTV",
  	"name": "TV",
  	"ip_address": "10.0.1.23",
  	"poll_status_interval": "60",
  	"model_year": 2016,
  	"has_ambilight": true,
  	"has_ssh": true,
  	"username": "deadbeef0815",
  	"password": "deadbeef0815deadbeef0815deadbeef0815deadbeef0815deadbeef0815",
  }
]
 ```

## Contributing

Contributions are welcome. To propose a change:

1. Open an issue first for larger changes or feature requests so the approach
   can be discussed.
2. Fork the repository and create a branch for your change.
3. Keep the change focused, and update the README or configuration examples
   when behavior or options change.
4. Test with Homebridge and a Philips TV if possible. In your pull request,
   describe what you tested and note any model or firmware limitations.
5. Open a pull request with a clear summary and any relevant issue links.

When reporting a problem, include your TV model and model year, firmware/API
details if known, relevant Homebridge logs, and steps to reproduce. Remove
credentials and other private information from logs and configuration examples.
