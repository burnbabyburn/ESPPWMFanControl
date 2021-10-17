# ESP PWM Fan Control
This application in its predefined configuration reads the temperature from an 10 kOhm NTC (wired to 3.3V via 150 kOhm pull-up resistor) connected to A0 and enables you to automatically control three PWM channels separately. Of course, all of these values are configurable in the settings file.

Let's dig into some more details:

- **PWM:** Since the PWM frequency is limited on the ESP, the range of possible values has been reduced from the default `1024` to five, which means `0 %`, `25 %`, `50 %`, `75 %` and `100 %`.  
This works well with the tested Arctic P12 PWM fans, even though we cannot measure the effective PWM frequency generated by the ESP8266 and it might be that the frequency still is too low (i.e. lower that `21 kHz`).  
A greater range may be possible, too, but has not been tested yet. This may also depend on the fans used and their tolerance in regards of the PWM frequency which may be lower than the by specification required `21~28 kHz`.

- **Settings:** Are stored in `JSON` format in `SPIFFS`.

- **The web interface:** Provides you full control over the fans, auto fan control settings, display settings and also an auto refresh function as well as raw JSON output for further use (and some extra stuff).

- **Display:** The application is capable of displaying the fan levels, temperature and the auto fan control state on an 128 x 64 px SSD1306 OLED connected via I2C.  
Because the mainly used cheap small OLEDs are not designed to live forever, there is also an option to limit the display on-time. This means, you can configure how many seconds per minute the display should actually be turned on.   
This is a quite simple approach for the beginning and may be replaced by a more complex solution in the future like triggering the display by a button / GPIO pin, MQTT or similar.

- **MQTT:** Basic MQTT code is included, but commented out since not needed (until now). Probably will be implemented some day.

- **Assets:** Since the RAM is very limited on AVRs, assets like JavaScript and CSS are stored via PROGMEM in flash.  
Of course, almost all of the strings are flash strings, too.  
The JavaScript code used in the web interface can be viewed [here](https://github.com/faeibson/ESPPWMFanControl/blob/master/assets/main.js). Same goes for the [CSS](https://github.com/faeibson/ESPPWMFanControl/blob/master/assets/main.css).

- **Beta: This project is in beta state. Testing is still needed and proper functionality not yet ensured.**

## How to install

Simply flash the project onto your ESP board through the Arduino IDE, or try out the [compiled binary](https://github.com/faeibson/ESPPWMFanControl/releases). You can flash it via [esptool](https://github.com/espressif/esptool) to your Wemos D1 mini, while NodeMCU and other similar boards with an ESP-12E or F (and maybe others) should work with that binary, too.

## Requirements

### Hardware
- An ESP8266
- NTC wired properly to `V+`, `GND` and `A0`
- Fans connected to the desired I/O pins (and `V+`/`GND`, of course) - preconfigured to `D7`, `D6` and `D3`
- (optional) An OLED connected to `SDA`/`SCL` (`D2`/`D1`)
- For an example wiring, please see the [schematic](/schematic) folder :)

### Software
- Arduino IDE (https://www.arduino.cc/en/Main/Software) (since 1.0-beta)
- Arduino IDE ESP8266 Libraries (https://github.com/esp8266/Arduino) (1.0-beta < 3.0.0)
- ArduinoJson (https://github.com/bblanchon/ArduinoJson) (since 1.0-beta)
- Brzo I2C (https://github.com/pasko-zh/brzo_i2c) (1.0-beta < 1.3.3)
- ESP8266 OLED driver for SSD1306 (https://github.com/ThingPulse/esp8266-oled-ssd1306) (since 1.0-beta)
- WiFiManager (https://github.com/tzapu/WiFiManager) (since 1.0-beta)
<!-- - ESP8266FS Plugin (https://github.com/esp8266/arduino-esp8266fs-plugin) (since 1.0-beta2)-->

<!-- ## Work in progress
- -->

## Possible future improvements and extensions

- Support multiple sensors.
- Support common digital temperature sensors. (DHT22, DS18B20, BME/BMP280)
- Support I2C in a general way.
- Improve frontend, separate configuration page, make things more responsive and stuff more thingy.
- Add the possibility to configure the analog temperature sensor specifications via frontend.
- Support other display types and make the output customizable, e.g. modify text and change the source sensor of temperature readings.
- Support MQTT input and output.
- Possibility to import the configuration file.
- More complex display configurability when to turn on and off.

## Screenshots

### Web interface

![The web interface](https://github.com/faeibson/ESPPWMFanControl/raw/master/screenshots/web_interface_1.0beta2.png "The web interface")

## JSON output

All of the relevant (and some not-so-relevant) variables and configuration data is accessible in JSON format under `/json`. The web interface itself uses it, too, to load and refresh the displayed values. The output is like:
```json
{
  "activeFanControlSet": 0,
  "automaticFanControlEnabled": true,
  "displayAddress": 60,
  "displayDurationPerMinute": 60,
  "displayEnabled": false,
  "displayFlipScreen": true,
  "displayIsOn": false,
  "id": "ESP",
  "ntcTemperature": 23.6,
  "ntcTemperatureTimeSinceMeasurement": 736,
  "pageRefreshTime": 0,
  "fanCount": 3,
  "fanPercentStepSize": 25,
  "maxFanControlSets": 9,
  "fanPins": [
    13,
    12,
    0
  ],
  "fanSpeeds": [
    100,
    100,
    100
  ],
  "fanSpeedsActive": [
    25,
    25,
    25
  ],
  "fanControlSets": [
    {
      "fanSpeeds": [
        25,
        25,
        25
      ],
      "tempThreshold": 0
    },
    {
      "fanSpeeds": [
        50,
        50,
        50
      ],
      "tempThreshold": 24
    },
    {
      "fanSpeeds": [
        75,
        75,
        75
      ],
      "tempThreshold": 26
    }
  ]
}
```

You can see that the fans are set to `25 %` each at the moment and there are three configuration sets for `26 °C`, `24 °C` and `below 24 °C` measured temperature. The automatic fan control mode is enabled, whereas the last manual fan speed setting was `100 %` for all three channels. The last NTC temperature was `23.6 °C` which was measured `736 ms` ago.  
Of course, since the project is in an early state, the naming and structure probably will still change and improve.
<!-- ![JSON output](https://github.com/faeibson/ESPPWMFanControl/raw/master/screenshots/json_output_1.0beta2.png "JSON output") -->

## Credits

The favicon has been made by [Stefania Servidio](https://thenounproject.com/term/water-temperature/523895/). The previously used icon can be found [here](https://www.pngitem.com/middle/iJTRwwb_engine-temperature-icon-png-clipart-png-download-water/).

## Changelog

### 1.0-beta2

- Code refactored, improved and cleaned up a lot
- Moved static content (favicon, JavaScript, CSS) to progmem assets
- Added download settings + restart ESP options
- Improved the frontend slightly. Will improve more ;)
- Fan control is not fixed to three sets anymore: Supports up to nine dynamically insertable/removable fan control sets each with independent speeds and temperature threshold
