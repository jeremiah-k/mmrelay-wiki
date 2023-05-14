M<>M Relay supports a plugin system to expand its capabilities.

We also encourage community members to contribute their own plugins to the project, which can be easily integrated into the existing framework to enhance the relay's functionality.


| Plugin      | Command         | Matrix Support | Radio Support | Description                                                        |
| ----------  | --------------- | -------------- | ------------- | ------------------------------------------------------------------ |
| `ping`      | `!ping`         | yes            | yes           | Check connectivity with the relay                                  |
| `health`    | `!health`       | yes            | no            | Show mesh health like avg battery, SNR, Air Util                   |
| `weather`   | `!weather`      | no             | yes           | Show weather forecast for a radio node using GPS location          |
| `telemetry` | `!batteryLevel` | yes            | no            | Graph of avg Mesh battery level for last 12 hours                  |
| `telemetry` | `!voltage`      | yes            | no            | Graph of avg Mesh battery voltage for last 12 hours                |
| `telemetry` | `!airUtilTx`    | yes            | no            | Graph of avg Mesh airUtilTx for last 12 hours                      |
| `mesh_relay`| N/A             | yes            | yes           | Relays radio packets between a Mesh and a Matrix room              |
| `map`       | `!map`          | yes            | no            | Map of mesh radio nodes. Supports `zoom` and `size` to customize   |

## How to enable a plugin

In order to use a plugin it must be added to the plugins section of the configuration file `config.yaml` file and given an `active:true` value. For example, to enable the Ping plugin ensure the following is set in the configuration file:

```
plugins:
  ping:
    active: true
```

## How to use plugin commands

A plugin is called by sending its command to the M<>M Relay bot.

For example to call the map plugin of the `Meshtastic Bot` message it `!map`:

![image](https://github.com/geoffwhittington/meshtastic-matrix-relay/assets/1770544/92e045eb-9989-42e2-b9bf-f8fb839661de)