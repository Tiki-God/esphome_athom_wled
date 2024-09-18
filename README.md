# esphome_athom_wled
ESPHome configuration for the Athom WLED Slim LED Strip Controller

## The Hardware
This is an example EPHome configuration for the Athom WLED LED strip controller.

It's an ESP32-C3 with 4MB of flash so should be quite capable.

https://www.athom.tech/blank-1/wled-slim-led-strip-controller
https://www.aliexpress.com/item/1005006974771273.html

## The Configuration
This should have been a nice simple EPSHome configuration file, but I ran in to
a few issues so figured I'd document them here for the world.

### GPIO Assignment
Taken from the manufacturers home page:

The button is assigned to GPIO9
The data ouptut for the LEDs is GPIO10

### Flash Mode
It took some digging but it turns out that this device needs a specific
flash mode setting otherwise it fails. So this is why we explicitly set dio.

```yaml
esphome:
  platformio_options:
    board_build.flash_mode: dio
```

[Bug reference](https://github.com/platformio/platform-espressif32/issues/622)

### WiFi Output Power
I've no idea why we need to explicitly set this, but without setting the WiFi
output power to 8.5dB the device fails to connect to WiFi at all, with errors
relating to invlid authentication. I've listed two links below for some refernce
to the issue.

[ref1](https://github.com/esphome/issues/issues/4893#issuecomment-1904992164)
[ref2](https://github.com/Aircoookie/WLED/blob/1ff667b7eff07ebad100e48eb871c698eab11a71/wled00/wled.cpp#L550)

```yaml
wifi:
  output_power: 8.5dB
```

### Logger Baud Rate
I initially ran in to problems relating to no output from the logger over the
serial connection. After some digging it seems that we need to set the baud
rate explicitly to 115200 and specify the UART to use.

```yaml
logger:
  baud_rate: 115200
  hardware_uart: UART0
```

[reference](https://github.com/athom-tech/esp32-configs/blob/e4912eb6e107509176f5e6d5b25c9aec9ff16639/athom-energy-monitor-x2.yaml#L67)

### FastLED
The version of FastLED currently used by ESPHome has compilation errors with
the ESP32-C3. They're likely fixed upstream, but for now just use the
esp32_rmt_led_strip platform.

See [here](https://github.com/esphome/issues/issues/4137) for some details.
