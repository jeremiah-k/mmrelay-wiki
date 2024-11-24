# Plugin Development Guide for Meshtastic-Matrix Relay

Welcome to the Meshtastic-Matrix Relay plugin development guide! This document will walk you through the basics of writing plugins for the relay system, including setting up a development environment, understanding the `BasePlugin` class, and creating your first plugin. This guide is meant to help you expand the functionality of the relay by creating custom plugins tailored to specific use cases.

## Prerequisites

To develop plugins for Meshtastic-Matrix Relay, you will need the following:

- Python 3.8+
- A working installation of the Meshtastic-Matrix Relay repository.
- Familiarity with Python and some experience with asynchronous programming.
- A text editor or IDE (e.g., VS Code, PyCharm).

## Understanding the Plugin System

Plugins in Meshtastic-Matrix Relay are Python classes that extend the functionality of the relay. All plugins inherit from a shared base class (`BasePlugin`) that provides essential methods and utilities for message handling, logging, and data persistence. By subclassing `BasePlugin`, you can write a plugin that interacts with either the Meshtastic mesh network or Matrix rooms, or both.

### Structure of the Base Plugin

The `BasePlugin` is designed to provide a consistent interface for all plugins. Here's a brief overview of some important features provided by `BasePlugin`:

- **Logging**: Each plugin has its own logger (`self.logger`) that helps with tracking actions and debugging.
- **Data Storage**: Methods like `store_node_data()`, `get_node_data()`, and `delete_node_data()` enable plugins to persistently store data specific to nodes.
- **Message Handling**: Plugins can react to incoming messages from Meshtastic or Matrix by implementing specific methods.

The two key methods that each plugin must implement are:

- `handle_meshtastic_message(packet, formatted_message, longname, meshnet_name)`
- `handle_room_message(room, event, full_message)`

These functions allow you to define how your plugin will handle incoming messages from Meshtastic nodes and Matrix rooms respectively.

## Creating Your First Plugin

Let's create a simple example plugin to get started. We will create a plugin named **HelloWorld** that logs "Hello world" when it receives a message from either Meshtastic or Matrix.

### Step 1: Set Up Your Plugin Directory

All plugins reside in the `plugins/` directory. If you want to create a new plugin, simply create a new file in this directory.

For this example, create a new file called `hello_world.py` in the `plugins/` directory:

```
plugins/
  - hello_world.py
```

### Step 2: Create the Plugin Class

Every plugin must inherit from `BasePlugin` and set its unique `plugin_name`. Below is the complete code for the HelloWorld plugin:

```
from plugins.base_plugin import BasePlugin

class Plugin(BasePlugin):
    plugin_name = "hello_world"

    async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
        self.logger.debug("Hello world, Meshtastic")

    async def handle_room_message(self, room, event, full_message):
        self.logger.debug("Hello world, Matrix")
```

### Step 3: Activate the Plugin in Your Configuration

To enable your new plugin, you will need to add it to your `config.yaml`. This is where the relay determines which plugins are active:

```
plugins:
  hello_world:
    active: true
```

The priority of the plugin is set internally within the plugin class itself by defining a `priority` attribute. By default, if you do not set a `priority` attribute in your plugin, it will use the default priority value set in the `BasePlugin` class. The lower the priority number, the earlier the plugin will be executed.

### Step 4: Running and Testing the Plugin

Once you've added the plugin to your configuration, restart the relay. If everything is set up correctly, you should see a line in your log indicating that the **HelloWorld** plugin has started:

```
DEBUG:Plugin:hello_world:Started with priority=10
```

You can then send messages via Meshtastic or Matrix to verify that the plugin is logging the expected "Hello world" messages.

## Advanced Topics

### Using Data Persistence

`BasePlugin` provides easy-to-use methods for saving and retrieving plugin-specific data. For instance, if your plugin needs to track statistics for each node, you can use:

```
# Store node data
self.store_node_data(meshtastic_id, node_data)

# Retrieve node data
node_data = self.get_node_data(meshtastic_id)
```

### Scheduling Background Tasks

If your plugin needs to perform periodic tasks, you can use the built-in scheduling capabilities from the `BasePlugin`. For example, the `start()` method in the base class provides a way to set up recurring background jobs:

```
def start(self):
    schedule.every(5).minutes.do(self.background_job)
```

### Handling Commands and Tagging Bots in Matrix

To make your plugin respond to specific commands in Matrix rooms, you need to handle user messages that tag the bot and provide a command. For commands to be processed, users must tag the bot using `@botname: !command`. Here's how you can extend your `handle_room_message()` method to support this functionality:

```
from matrix_utils import bot_command

async def handle_room_message(self, room, event, full_message):
    # Check if the message is a command directed to the bot
    if bot_command(self.plugin_name, full_message):
        if "!status" in full_message:
            await self.send_matrix_message(room_id=room.room_id, message="System is running smoothly.")
        elif "!hello" in full_message:
            await self.send_matrix_message(room_id=room.room_id, message="Hello from the plugin!")
```

This allows the plugin to handle messages that specifically tag it in the form `@botname: !command`. The `bot_command()` helper function will help determine if the bot's name is included in the message, ensuring that only relevant messages are processed.

### Handling Meshtastic-Specific Commands

You can also create specific commands for handling messages coming from the Meshtastic network. The `handle_meshtastic_message()` function can be modified to parse commands from Meshtastic nodes:

```
async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
    # Assuming the packet contains a text command
    if formatted_message.strip().lower() == "!ping":
        self.logger.debug(f"Received ping command from {longname}")
        # Respond to the ping command
        await self.send_mesh_message(meshnet_name=meshnet_name, message="Pong from the relay!")
```

## Best Practices

1. **Use Logging Liberally**: Make use of `self.logger` to record key events, errors, or other relevant information for debugging and monitoring.
2. **Keep Plugins Modular**: Aim for each plugin to handle a distinct piece of functionality. This makes it easier to maintain and debug.
3. **Avoid Blocking Operations**: Since the relay is an asynchronous application, be careful not to use blocking calls that could delay message handling.
4. **Respect Plugin Priorities**: Set plugin priorities appropriately by defining the `priority` attribute within your plugin class to control the order in which messages are processed by different plugins.

## Next Steps

Now that you know the basics, consider adding more complex features to your plugin, such as interacting with external APIs, responding to specific commands, or handling more detailed data from the Meshtastic network. Check out existing plugins, such as `nodes_plugin.py` or `map_plugin.py`, for more ideas and examples.

If you have any questions or run into any issues, feel free to reach out or open a discussion in the project's community channels.

Happy coding!


