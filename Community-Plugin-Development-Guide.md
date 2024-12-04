Welcome to the MMRelay plugin development guide! This document will walk you through the basics of writing plugins for the relay system, including setting up a development environment, understanding the `BasePlugin` class, and creating your first plugin. This guide is meant to help you expand the functionality of the relay by creating custom plugins tailored to specific use cases.

## Prerequisites

To develop plugins for MMRelay, you will need the following:

- Python 3.8+
- A working installation of the MMRelay application.
- Familiarity with Python and some experience with asynchronous programming (If you are new to these, this is a good way to learn!).
- A text editor or IDE (e.g., VS Code, PyCharm. I prefer [VSCodium](https://vscodium.com/).).

## Understanding the Plugin System

Plugins in MMRelay are Python classes that extend the functionality of the relay. All plugins inherit from a shared base class (`BasePlugin`) that provides essential methods and utilities for message handling, logging, and data persistence. By subclassing `BasePlugin`, you can write a plugin that interacts with either the Meshtastic meshnet or Matrix rooms, or both.

### Structure of the Base Plugin

The `BasePlugin` is designed to provide a consistent interface for all plugins. Here's a brief overview of some important features provided by `BasePlugin`:

- **Logging**: Each plugin has its own logger (`self.logger`) that helps with tracking actions and debugging.
- **Data Storage**: Methods like `store_node_data()`, `get_node_data()`, and `delete_node_data()` enable plugins to persistently store data specific to nodes.
- **Message Handling**: Plugins can react to incoming messages from Meshtastic or Matrix by implementing specific methods.
- **Configuration Options**: Plugins can access configuration options like `plugin_response_delay` and `channels` from the `config.yaml` file.

The two key methods that each plugin must implement are:

- `handle_meshtastic_message(packet, formatted_message, longname, meshnet_name)`
- `handle_room_message(room, event, full_message)`

These functions allow you to define how your plugin will handle incoming messages from Meshtastic nodes and Matrix rooms respectively.

## Creating Your First Plugin

Let's create a simple example plugin to get started. We will create a minimalist plugin named **HelloWorld** that logs "Hello world" when it receives a message from either Meshtastic or Matrix.

### Step 1: Set Up Your Plugin Repository

Plugins should reside in their own project repositories. To create a new plugin, start by creating your own project repository on the code hosting platform of your choice (GitHub, GitLab, Codeberg, etc.). Clone your new repo to your local environment and open it in your preferred editor. Inside your cloned project, create a new file for the plugin.

_Note: If you want an easy way to get started right away, you can fork this template repository [mmr-plugin-template](https://github.com/jeremiah-k/mmr-plugin-template) and add your own code._

For this example, create a new file called `hello_world.py` in your project repository.

### Step 2: Create the Plugin Class

Every plugin must inherit from `BasePlugin` and set its unique `plugin_name`. Below is the complete code for the HelloWorld plugin:

```python
from plugins.base_plugin import BasePlugin

class Plugin(BasePlugin):
    plugin_name = "hello_world"

    async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
        self.logger.debug("Hello world, Meshtastic")
        return False  # Indicate that we did not handle the message

    async def handle_room_message(self, room, event, full_message):
        self.logger.debug("Hello world, Matrix")
        return False  # Indicate that we did not handle the message
```

### Step 3: Activate the Plugin in Your Configuration

To enable your new plugin, you will need to add it to your `config.yaml`. This is where the relay determines which plugins are active:

```yaml
custom-plugins:
  hello_world:
    active: true
```

If your plugin is hosted in a repository and you want the relay to clone it automatically, you can specify the repository and tag:

```yaml
community-plugins:
  hello_world:
    active: true
    repository: https://github.com/YourUsername/HelloWorld.git
    tag: main
```

### Step 4: Running and Testing the Plugin

Once you've added the plugin to your configuration, restart the relay. If everything is set up correctly, you should see a line in your log indicating that the **HelloWorld** plugin has started:

```bash
DEBUG:Plugin:hello_world:Started with priority=10
```

You can then send messages via Meshtastic or Matrix to verify that the plugin is logging the expected "Hello world" messages.

## Minimalist Plugin Example

If you're looking for a minimalist example to get started without worrying about handling channels or direct messages (DMs), here's a simple plugin that responds to a specific command:

```python
from plugins.base_plugin import BasePlugin

class Plugin(BasePlugin):
    plugin_name = "simple_responder"

    async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
        if "decoded" in packet and "text" in packet["decoded"]:
            message = packet["decoded"]["text"].strip()

            if message == "!hello":
                from meshtastic_utils import connect_meshtastic
                meshtastic_client = connect_meshtastic()

                # Respond with a greeting
                meshtastic_client.sendText(text="Hello!", channelIndex=0)
                return True  # Indicate that we handled the message
        return False  # Indicate that we did not handle the message

    async def handle_room_message(self, room, event, full_message):
        return False  # Not handling Matrix messages in this plugin
```

This example avoids the complexities of channel and DM handling, making it easier for beginners to get started.

## Advanced Topics

As you become more comfortable with plugin development, you can explore more advanced features to enhance your plugins.

### Handling Direct Messages (DMs) and Channel-Specific Responses

Plugins can be configured to respond differently to direct messages (DMs) and messages in specific channels. The behavior is controlled by the `channels` setting in your plugin's configuration.

#### Responding Only to Direct Messages (DMs)

If you want your plugin to respond **only** to DMs and ignore all channel messages, you can set the `channels` list to be empty:

```yaml
plugins:
  my_plugin:
    active: true
    channels: []  # Empty list means the plugin will only respond to DMs
```

In your plugin, you can check whether to respond to a message based on the channel and whether it's a DM using the `is_channel_enabled` method:

``python
if not self.is_channel_enabled(channel, is_direct_message=is_direct_message):
    self.logger.debug(f"Channel {channel} not enabled for plugin '{self.plugin_name}'")
    return False
```

#### Responding to All Mapped Channels

If you want your plugin to respond to all mapped channels (channels defined in `matrix_rooms`), you can either omit the `channels` setting or comment it out:

```yaml
plugins:
  my_plugin:
    active: true
    # channels: [0,1,3,4]  # Commented out or omitted; plugin responds to all mapped channels
```

By default, if `channels` is not specified, the plugin will respond to all mapped channels. The `is_channel_enabled` method handles this logic internally.

#### Responding to Specific Channels

If you want your plugin to respond only to specific channels, you can list them in the `channels` setting:

```yaml
plugins:
  my_plugin:
    active: true
    channels: [0,1,3,4]  # List of Meshtastic channels the plugin should respond to
```

#### Handling Direct Messages (DMs)

By default, plugins will always respond to DMs if they are active, regardless of the `channels` configuration. The `is_channel_enabled` method in `BasePlugin` ensures that DMs are always enabled for the plugin:

```python
def is_channel_enabled(self, channel, is_direct_message=False):
    if is_direct_message:
        return True  # Always respond to DMs if the plugin is active
    else:
        return channel in self.channels
```

When processing a message, determine if it is a DM and pass the `is_direct_message` parameter:

```python
# Determine if the message is a direct message
toId = packet.get("to")
myId = meshtastic_client.myInfo.my_node_num  # Relay's own node number

if toId == myId:
    is_direct_message = True
else:
    is_direct_message = False

if not self.is_channel_enabled(channel, is_direct_message=is_direct_message):
    return False
```

This ensures that your plugin can respond appropriately to DMs and channel messages based on your configuration.

### Using Data Persistence

`BasePlugin` provides easy-to-use methods for saving and retrieving plugin-specific data. For instance, if your plugin needs to track statistics for each node, you can use:

```python
# Store node data
self.store_node_data(meshtastic_id, node_data)

# Retrieve node data
node_data = self.get_node_data(meshtastic_id)
```

### Scheduling Background Tasks

If your plugin needs to perform periodic tasks, you can use the built-in scheduling capabilities from the `BasePlugin`. For example, the `start()` method in the base class provides a way to set up recurring background jobs:

```python
def start(self):
    schedule.every(5).minutes.do(self.background_job)
```

### Handling Commands and Tagging Bots in Matrix

To make your plugin respond to specific commands in Matrix rooms, you need to handle user messages that tag the bot and provide a command. For commands to be processed, users must tag the bot using `@botname: !command`. Here's how you can extend your `handle_room_message()` method to support this functionality:

```python
async def handle_room_message(self, room, event, full_message):
    # Check if the message is a command directed to the bot
    if self.matches(full_message):
        if "!status" in full_message:
            await self.send_matrix_message(room_id=room.room_id, message="System is running smoothly.")
        elif "!hello" in full_message:
            await self.send_matrix_message(room_id=room.room_id, message="Hello from the plugin!")
        return True  # Indicate that we handled the message
    return False  # Indicate that we did not handle the message
```

The `self.matches(full_message)` method helps determine if the bot's name is included in the message, ensuring that only relevant messages are processed.

### Handling Meshtastic-Specific Commands

You can also create specific commands for handling messages coming from the Meshtastic network. The `handle_meshtastic_message()` function can be modified to parse commands from Meshtastic nodes:

```python
async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
    if "decoded" in packet and "text" in packet["decoded"]:
        message = packet["decoded"]["text"].strip()
        channel = packet.get("channel", 0)

        from meshtastic_utils import connect_meshtastic
        meshtastic_client = connect_meshtastic()

        # Determine if the message is a direct message
        toId = packet.get("to")
        myId = meshtastic_client.myInfo.my_node_num  # Relay's own node number

        if toId == myId:
            is_direct_message = True
        else:
            is_direct_message = False

        if not self.is_channel_enabled(channel, is_direct_message=is_direct_message):
            return False

        if message == "!ping":
            # Wait for the response delay
            await asyncio.sleep(self.get_response_delay())

            fromId = packet.get("fromId")

            if is_direct_message:
                # Respond via DM
                meshtastic_client.sendText(
                    text="pong",
                    destinationId=fromId,
                )
            else:
                # Respond in the same channel
                meshtastic_client.sendText(
                    text="pong",
                    channelIndex=channel,
                )
            return True  # Indicate that we handled the message
    return False  # Indicate that we did not handle the message
```

**Note on Response Delay**: If your plugin automatically responds to mesh commands, it's important to respect the `plugin_response_delay` configuration option. You can retrieve the configured delay using `self.get_response_delay()` and apply it before sending your response, as shown in the example above. This helps in managing network traffic and prevents overwhelming the mesh network with rapid replies.

## Best Practices

1. **Use Logging Liberally**: Make use of `self.logger` to record key events, errors, or other relevant information for debugging and monitoring.
2. **Keep Plugins Modular**: Aim for each plugin to handle a distinct piece of functionality. This makes it easier to maintain and debug.
3. **Avoid Blocking Operations**: Since the relay is an asynchronous application, be careful not to use blocking calls that could delay message handling.
4. **Respect Plugin Priorities**: Set plugin priorities appropriately by defining the `priority` attribute within your plugin class to control the order in which messages are processed by different plugins.
5. **Handle Response Delays**: If your plugin sends automatic responses, use `await asyncio.sleep(self.get_response_delay())` to respect the configured response delay.
6. **Manage Channel Responses**: Use `self.is_channel_enabled(channel, is_direct_message=is_direct_message)` to control where your plugin responds and to ensure it handles DMs appropriately.

## Next Steps

Now that you know the basics, consider adding more complex features to your plugin, such as interacting with external APIs, responding to specific commands, or handling more detailed data from the Meshtastic network. Check out existing plugins, such as `nodes_plugin.py` or `weather_plugin.py`, for more ideas and examples.

If you have any questions or run into any issues, feel free to ask in the project's Matrix room [#mmrelay:meshnet.club](https://matrix.to/#/#mmrelay:meshnet.club).

Happy coding!
