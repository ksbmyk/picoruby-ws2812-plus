# picoruby-ws2812-plus

A C-accelerated WS2812 LED driver gem for PicoRuby. This is the high-performance variant of [picoruby-ws2812](https://github.com/ksbmyk/picoruby-ws2812), optimized for large LED counts and animations.

## picoruby-ws2812 vs picoruby-ws2812-plus

| | picoruby-ws2812 | picoruby-ws2812-plus |
|---|---|---|
| Implementation | Pure Ruby | Ruby + C extension |
| Installation | RuntimeGem (no build required) | Build-time embedding |
| Performance | Standard | Faster (C-accelerated `_convert`) |
| Use case | Small LED counts, simple patterns | Large LED counts, animations |

Both gems provide the same API. Only the `require` statement differs.

## Installation

Add the following line to your build configuration:

### ESP32

e.g., `build_config/xtensa-esp.rb`

```ruby
conf.gem github: 'ksbmyk/picoruby-ws2812-plus', branch: 'main'
```

### RP2040/RP2350 (R2P2)

Note: Requires a recent PicoRuby master branch that includes `picoruby-pio`.

e.g., `build_config/r2p2-picoruby-pico2.rb`

```ruby
conf.gem github: 'ksbmyk/picoruby-ws2812-plus', branch: 'main'
```

## Usage

```ruby
require 'ws2812-plus'

# Initialize (specify GPIO pin and number of LEDs)
led = WS2812.new(pin: 25, num: 64)

# Set individual LED colors (writes to internal buffer)
led.set_rgb(0, 255, 0, 0)      # LED 0: Red (R, G, B: 0-255)
led.set_hex(1, 0x00FF00)       # LED 1: Green (24-bit hex)
led.set_hsb(2, 240, 100, 100)  # LED 2: Blue (H: 0-360, S: 0-100, B: 0-100)

# Fill all LEDs with the same color
led.fill(255, 255, 255)        # All white

# Send buffer to LEDs
led.show

# Turn off all LEDs
led.clear

# Clean up when done
led.close
```

## API Reference

### Constructor

```ruby
WS2812.new(pin:, num:, order: :grb)
```

**Parameters:**

- `pin` — GPIO pin number (Integer)
- `num` — Number of LEDs (Integer)
- `order` — Color order (Symbol, optional). `:grb` (default, standard WS2812) or `:rgb`

### Methods

| Method | Description |
|--------|-------------|
| `set_rgb(index, r, g, b)` | Set LED color using RGB values (0-255) |
| `set_hex(index, hex)` | Set LED color using 24-bit hex (e.g. `0xFF0000`) |
| `set_hsb(index, h, s, b)` | Set LED color using HSB (H: 0-360, S: 0-100, B: 0-100) |
| `fill(r, g, b)` | Fill all LEDs with the same RGB color |
| `brightness` | Get current brightness (0-100) |
| `brightness=` | Set brightness (0-100, default: 5) |
| `show` | Send buffer to LEDs |
| `clear` | Turn off all LEDs (fills buffer with 0 and calls `show`) |
| `close` | Release driver resources |

### Notes

- Color values are written to an internal buffer. Call `show` to transmit the buffer to the LEDs.
- Out-of-range index values are silently ignored.
- RGB values are masked to 0-255.
- Brightness is applied at `show` time — buffer values are not modified.
- Default brightness is 5 (5%) for safety. Running many LEDs at full brightness draws significant current and can exceed USB power limits.

## License

MIT License
