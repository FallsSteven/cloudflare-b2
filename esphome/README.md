# ESPHome – Seeed Studio XIAO Expansion Board

ESPHome config for the **Seeed Studio Expansion Board Base for XIAO**, with a
XIAO ESP32-C3, ESP32-S3 or ESP32-C6.

| Feature on the board          | XIAO pin | ESPHome                                   |
|-------------------------------|----------|-------------------------------------------|
| 0.96" OLED SSD1306 (0x3C)     | D4/D5    | `display: ssd1306_i2c`, 2 pages           |
| PCF8563 RTC (0x51)            | D4/D5    | `time: pcf8563`, synced from Home Assistant |
| Grove I2C ×2                  | D4/D5    | shared `i2c` bus `bus_i2c`                |
| Grove UART                    | D6 TX / D7 RX | `uart` → "UART Last Line" sensor + "UART Send" text |
| Grove A0/D0                   | D0       | `adc` "Grove A0 Voltage" (or a digital input, see the comment) |
| User button                   | D1       | short press = next OLED page, long press = beep |
| Passive buzzer                | D3 (A3)  | `rtttl` + "Buzzer Beep" button            |

| File                      | XIAO      |
|---------------------------|-----------|
| `xiao-expansion.yaml`     | ESP32-C3 (change its `board:` line to `boards/xiao-esp32s3.yaml` for an S3) |
| `xiao-expansion-c6.yaml`  | ESP32-C6  |

Both include `common/expansion-board.yaml`, which holds everything the boards share.
The pin numbers for each chip are in `boards/`.

### ESP32-C6 extras
- The **RF switch** is turned on at boot (GPIO3 low). Without this, Wi-Fi is weak or doesn't work.
- The **"External Antenna"** switch (GPIO14) chooses between the on-board antenna and a U.FL antenna. The default is the on-board antenna.
- The **"XIAO LED"** light controls the yellow LED on the XIAO itself (GPIO15).

## Use

```bash
cp secrets.yaml.example secrets.yaml   # fill in Wi-Fi + API key
esphome run xiao-expansion.yaml      # ESP32-C3
esphome run xiao-expansion-c6.yaml   # ESP32-C6
```

## Notes
- The logger uses the USB-Serial-JTAG port, so D6/D7 stay free for the Grove UART.
- Set the Grove UART baud rate with the `uart_baud` substitution (9600 by default).
- To add a Grove I2C sensor, add it with `i2c_id: bus_i2c`. Avoid sensors at 0x3C or 0x51, which the OLED and RTC use.
- ESP32-C3 only: ESPHome warns that GPIO2 (D0) is a strapping pin. This is harmless while
  nothing drives D0 low during boot.
