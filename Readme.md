# ZMK Driver ext-power-transient

ZMK driver which implements ext-power api.

The implementation is very similar to official `zmk,ext-power-generic` driver. But intended to be frequently updated and doesn't save power state in persistent setting.

The driver is implemented to minimize LED module IC power consumption in battery powered keyboard.

By using it with https://github.com/cormoran/zmk-driver-animation, LED module power enabled only during showing indicator animation.

## usage

In your config/west.yml:

```yaml
manifest:
  remotes:
    ...
    - name: cormoran
      url-base: https://github.com/cormoran
    ...
  projects:
    ...
    - name: zmk-driver-ext-power-transient
      remote: cormoran
      revision: main
    ...
```

In your `<keyboard>.dtsi`,

```dts
\ {
    led_power:led_power {
        compatible = "zmk,ext-power-transient";
        control-gpios = <&gpio0 9 (GPIO_ACTIVE_LOW)>; // NFC1 in XIAO nRF52840
    };
}
```

You can use `&led_power` to field which supports `ext-power` interface driver.
