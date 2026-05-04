# ZMK Driver ext-power-transient

![ZMK Version](https://img.shields.io/badge/ZMK-main%20%7C%20v0.3-blue)
[![Test](https://github.com/cormoran/zmk-driver-ext-power-transient/actions/workflows/zmk-module.yml/badge.svg?branch=main)](https://github.com/cormoran/zmk-driver-ext-power-transient/actions/workflows/zmk-module.yml)
[![Devcontainer](https://github.com/cormoran/zmk-driver-ext-power-transient/actions/workflows/devcontainer.yml/badge.svg?branch=main)](https://github.com/cormoran/zmk-driver-ext-power-transient/actions/workflows/devcontainer.yml)

ZMK driver which implements the ext-power API without persistent storage.

The implementation is similar to the official `zmk,ext-power-generic` driver, but is
intended to be frequently enabled/disabled and does not save power state in persistent settings.

The driver is designed to minimize LED module IC power consumption in battery powered keyboards.
By using it with [zmk-driver-animation](https://github.com/cormoran/zmk-driver-animation),
the LED module power is enabled only during indicator animations.

## Module User Guide

### 1. Add dependency to your `config/west.yml`

```yaml
manifest:
  remotes:
    - name: cormoran
      url-base: https://github.com/cormoran
  projects:
    - name: zmk-driver-ext-power-transient
      remote: cormoran
      revision: main # or latest commit hash
```

### 2. Enable in `config/<shield>.conf`

```conf
CONFIG_ZMK_DRIVER_EXT_POWER_TRANSIENT=y
```

### 3. Add device to `<keyboard>.dtsi` or overlay

```dts
/ {
    led_power: led_power {
        compatible = "zmk,ext-power-transient";
        control-gpios = <&gpio0 9 (GPIO_ACTIVE_LOW)>; // NFC1 on XIAO nRF52840
    };
};
```

You can then use `&led_power` wherever an `ext-power` interface driver is accepted.

## Module Development Guide

### Setup for running tests

#### Option 1: Isolated directory layout (recommended for CI and devcontainer)

```bash
git clone https://github.com/cormoran/zmk-driver-ext-power-transient
cd zmk-driver-ext-power-transient
west init -l west --mf west-test-isolated.yml
west update --narrow
west zephyr-export
```

#### Option 2: West workspace directory layout

```bash
mkdir west-workspace
cd west-workspace
git clone https://github.com/cormoran/zmk-driver-ext-power-transient
west init -l . --mf west/west-test-workspace.yml
west update --narrow
west zephyr-export
```

### Pre-commit

Every commit needs to pass pre-commit verification (formatting and tests).

```bash
pip install pre-commit
pre-commit install

# Run pre-commit manually on all files
pre-commit run --all-files
# Run for git staged files only
pre-commit run
```

### Running Tests

```bash
# Run unit test + build test and verify results
python3 -m unittest
# Run build test directly (builds for xiao_ble)
west zmk-build tests/zmk-config
# Run unit test directly (native_sim)
west zmk-test tests -m .
```
