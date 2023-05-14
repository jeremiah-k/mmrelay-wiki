M<>M Relay supports a plugin system to expand its capabilities.

We also encourage community members to contribute their own plugins to the project, which can be easily integrated into the existing framework to enhance the relay's functionality.


| Plugin     | Command         | Matrix Support | Radio Support | Description                                                        |
| ---------- | --------------- | -------------- | ------------- | ------------------------------------------------------------------ |
| Ping       | `!ping`         | yes            | yes           | Check connectivity with the relay                                  |
| Health     | `!health`       | yes            | no            | Show mesh health like avg battery, SNR, Air Util                   |
| Weather    | `!weather`      | no             | yes           | Show weather forecast for a radio node using GPS location          |
| Telemetry  | `!batteryLevel` | yes            | no            | Graph of avg Mesh battery level for last 12 hours                  |
| Telemetry  | `!voltage`      | yes            | no            | Graph of avg Mesh battery voltage for last 12 hours                |
| Telemetry  | `!airUtilTx`    | yes            | no            | Graph of avg Mesh airUtilTx for last 12 hours                      |
| Mesh Relay | N/A             | yes            | yes           | Relays radio packets between a Mesh and a Matrix room              |
| Map        | `!map`          | yes            | no            | Map of mesh radio nodes. Supports `zoom` and `size` to customize   |

