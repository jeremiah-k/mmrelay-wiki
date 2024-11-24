M<>M Relay supports a plugin system to expand its capabilities.

Core plugins:

| Plugin      | Command         | Matrix Support | Radio Support | Description                                                        |
| ----------  | --------------- | -------------- | ------------- | ------------------------------------------------------------------ |
| `ping`      | `!ping`         | yes            | yes           | Check connectivity with the relay                                  |
| `health`    | `!health`       | yes            | no            | Show mesh health like avg battery, SNR, Air Util                   |
| `weather`   | `!weather`      | no             | yes           | Show weather forecast for a radio node using GPS location          |
| `telemetry` | `!batteryLevel` | yes            | no            | Graph of avg Mesh battery level for last 12 hours                  |
| `telemetry` | `!voltage`      | yes            | no            | Graph of avg Mesh battery voltage for last 12 hours                |
| `telemetry` | `!airUtilTx`    | yes            | no            | Graph of avg Mesh airUtilTx for last 12 hours                      |
| `mesh_relay`|                 | yes            | yes           | Relays radio packets between a Mesh and a Matrix room              |
| `map`       | `!map`          | yes            | no            | Map of mesh radio nodes. Supports `zoom` and `size` to customize   |

_Note: As of 11/24/24 the map_plugin needs maintenance before it is functional again, also the `mesh_relay` plugin is experimental and needs more work before its operational._

## How to enable a plugin

In order to use a plugin it must be added to the plugins section of the configuration file `config.yaml` file and given an `active:true` value.

For example, to enable the Ping plugin ensure the following is set in the configuration file:

```
plugins:
  ping:
    active: true
```

When a plugin is activated they appear in the startup logs. The `map`, `ping` and `weather` plugins are loaded in the following example,

```
2023-05-13 23:20:51 -0400 INFO:Meshtastic:Connecting to host meshtastic.local ...
2023-05-13 23:20:54 -0400 INFO:Plugins:Loaded map
2023-05-13 23:20:54 -0400 INFO:Plugins:Loaded ping
2023-05-13 23:20:54 -0400 INFO:Plugins:Loaded weather
2023-05-13 23:20:54 -0400 INFO:Matrix:Connecting ...
```

## How to use plugin commands

A plugin is called by sending its command to the M<>M Relay bot.

For example to call the map plugin of the `Meshtastic Bot` message it `!map`:

![image](https://github.com/geoffwhittington/meshtastic-matrix-relay/assets/1770544/92e045eb-9989-42e2-b9bf-f8fb839661de)

## Map plugin

The Map plugin displays mesh radio nodes on a world map. Node locations are randomly placed up to 10km away from their actual location in order to hide their true location. By default the generated map has `size` width=1000px and height=1000px. It has a `zoom` value of 8. Lower zoom values show less detail and more of the Earth.

Here are some examples of how to use the plugin:

* `!map`: Shows the radio nodes on a world map with a zoom of `8` default size of width=`1000`px height=`1000`px
* `!map size=800,900`: Shows the radio nodes on a world map of size width=`800`px height=`900`px
* `!map zoom=5 size=800,900`: Shows the radio nodes on a world map zoomed out. It has size width=`800`px height=`900`px

We also encourage users to contribute their own plugins to the project through our new (Community Plugins)[https://github.com/geoffwhittington/meshtastic-matrix-relay/wiki/Community-Plugins] framework.