M<>M Relay includes several built-in plugins that extend its functionality. These plugins can be enabled in your configuration file.

## Available Core Plugins

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
| `nodes`     | `!nodes`        | yes            | no            | List all nodes in the mesh network                                 |

_Note: The `mesh_relay` plugin is experimental and needs more work before it's fully operational._

## Plugin Locations

As of v1.0.0, plugins are stored in standardized locations:

- **Core Plugins**: Included in the package
- **Custom Plugins**: `~/.mmrelay/plugins/custom/`
- **Community Plugins**: `~/.mmrelay/plugins/community/`

## Enabling Plugins

To use a plugin, add it to the `plugins` section of your configuration file (`~/.mmrelay/config.yaml`) and set `active: true`:

```yaml
plugins:
  ping:
    active: true
  map:
    active: true
  weather:
    active: true
```

When plugins are activated, they appear in the startup logs:

```
INFO: Plugins loaded: map, ping, weather
```

## Using Plugin Commands

Call a plugin by sending its command to the M<>M Relay bot in your Matrix room.

For example, to use the map plugin, send:

```
!map
```

## Map Plugin

The Map plugin displays mesh radio nodes on a world map. Node locations are randomly placed up to 10km away from their actual location to protect privacy.

### Map Options

- **Default**: `!map` - Shows nodes with zoom level 8 and size 1000x1000px
- **Custom Size**: `!map size=800,900` - Sets width=800px, height=900px
- **Custom Zoom**: `!map zoom=5` - Lower zoom values show less detail and more area
- **Combined**: `!map zoom=5 size=800,900` - Custom zoom and size

## Creating Your Own Plugins

We encourage users to contribute their own plugins. See the [Community Plugin Development Guide](Community-Plugin-Development-Guide.md) for details.

For a list of available community plugins, see the [Community Plugin List](Community-Plugin-List.md).
