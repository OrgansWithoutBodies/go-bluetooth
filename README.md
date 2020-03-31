# go-bluetooth

Go bluetooth API for Linux-based Bluez DBus interface.

[![GoDoc](https://godoc.org/github.com/muka/go-bluetooth?status.svg)](https://godoc.org/github.com/muka/go-bluetooth)

<img align="center" width="90" src="./gopher.png">

## Getting started

1. Clone the repository

  `git clone https://github.com/muka/go-bluetooth.git`

1. Retrieve the bluetooth API and generate GO code

  `make all`

2. Run an example

  `go-bluetooth discovery`

The `examples/` folder offer an API overview.

## Features

The library is a wrapper to the Bluez DBus API and some high level API to ease the interaction.

High level features supported:

- [x] Client code generation from bluez documentation
- [x] Shell wrappers for `rfkill`, `btmgmt`, `hciconfig`, `hcitool`
- [x] An `hci` basic API (from a fork of [go-ble/ble](https://github.com/muka/ble))
- [x] Expose bluetooth service from go code [*unstable*]
- [x] Pairing and authentication support (via agent)
- [x] Beaconing send & receive (iBeacon and Eddystone)

## Code generation

The go Bluez API is generated from `bluez` documentation, run `make gen` to re-generate go sources. There is also a commodity bluez JSON file available in the root folder for reference.

Code generation will not overwrite existing files, run `make gen/clean` to remove generated content.

Generated code has `gen_` prefix. If an API file exists with the same filename but without the prefix, generation will be skipped for that API.

## DBus configuration setup

In order to interact with DBus, propert configurations must be installed in the system. For a development setup, the repository provides example configurations.

```sh
  cd $GOPATH/src/github.com/muka/go-bluetooth
  sudo ln -s `pwd`/scripts/dbus-go-bluetooth-service.conf /etc/dbus-1/system.d/
  sudo ln -s `pwd`/scripts/dbus-go-bluetooth-dev.conf /etc/dbus-1/system.d/
  # Reload dbus to load new policies:
  # via dbus
  # dbus-send --system --type=method_call --dest=org.freedesktop.DBus / org.freedesktop.DBus.ReloadConfig
  # via systemctl
  systemctl reload dbus
```

## Requirements

The library is tested with

- golang `1.14.1`
- bluez bluetooth `v5.50`

### Development notes

-  

-   Give access to `hciconfig` to any user and avoid `sudo` (may have [security implications](https://www.insecure.ws/linux/getcap_setcap.html))

    ```
    sudo setcap 'cap_net_raw,cap_net_admin+eip' `which hciconfig`
    ```
- Monitor Bluetooth activity

  `sudo btmon`

- Monitor DBus activity

    `sudo dbus-monitor --system "type=error"`

- Start `bluetoothd` with experimental features and verbose debug messages `make bluetoothd`

- Enable LE advertisement (on a single pc ensure to use at least 2x bluetooth adapter)

  ```bash

    sudo btmgmt -i 0 power off
    sudo btmgmt -i 0 name "my go app"
    sudo btmgmt -i 0 le on    
    sudo btmgmt -i 0 connectable on
    sudo btmgmt -i 0 advertising on
    sudo btmgmt -i 0 power on

  ```

## Contributing

Feel free to open an issue and/or a PR to contribute. If you would like to help improve the library without coding directly, you can also consider to contribute by providing some hardware to test on.

## References

- [Standard GATT characteristics descriptions](https://www.bluetooth.com/specifications/gatt/)
- [bluez git](https://git.kernel.org/cgit/bluetooth/bluez.git/tree/doc)
- [GATT services specs](https://www.bluetooth.com/specifications/gatt/services)
- [A C++ implementation](https://github.com/nettlep/gobbledegook)
- [DBus specifications](https://dbus.freedesktop.org/doc/dbus-specification.html#type-system)
- [SensorTag specs](http://processors.wiki.ti.com/images/a/a8/BLE_SensorTag_GATT_Server.pdf)

## License

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

   http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
