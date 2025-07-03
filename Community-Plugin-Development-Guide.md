Welcome to the M<>M Relay plugin development guide! This document will get you started with writing plugins for the relay system. Whether you want to add weather reports, track telemetry data, or create custom commands, this guide will show you how to extend the relay's functionality with your own plugins.

We'll cover everything from setting up your development environment to creating and deploying your first plugin. The examples are practical and ready to use, so you can start building right away.

## Changes in v1.0

With the release of v1.0, there are some important changes to plugin development:

- **New Plugin Locations**: Plugins are now stored in standardized locations:
  - Core plugins: Included in the package
  - Custom plugins: `~/.mmrelay/plugins/custom/`
  - Community plugins: `~/.mmrelay/plugins/community/`

- **Standardized Data Storage**: Plugin data should be stored in standardized locations:
  - Database data: Use `BasePlugin` methods like `store_node_data()` and `get_node_data()`
  - File data: Use `self.get_plugin_data_dir()` to get `~/.mmrelay/data/plugins/<plugin_name>/`

- **Absolute Imports**: The codebase now uses absolute imports. When developing plugins, you should use:
  ```python
  from mmrelay.plugins.base_plugin import BasePlugin
  from mmrelay.meshtastic_utils import connect_meshtastic
  from mmrelay.matrix_utils import bot_command
  ```

- **Matrix Client Usage**: Always use the `send_matrix_message()` method from `BasePlugin` for sending Matrix messages. This method handles client initialization and error checking automatically. Never call `connect_matrix()` directly in your plugins, as this will reinitialize the client and cause unnecessary credential reloading.

- **Configuration Path**: Configuration is now stored at `~/.mmrelay/config.yaml` by default

- **Separate Branch and Tag Parameters**: Community plugins now support separate `branch:` and `tag:` parameters in config.yaml, allowing for more precise version control.

- **Automatic Dependency Installation**: The plugin system now automatically detects and installs dependencies from requirements.txt files, with support for both pip and pipx installations.

- **Improved Error Handling**: Better error messages and logging for plugin loading issues, with specific guidance for resolving dependency problems.

## Prerequisites

To develop plugins for M<>M Relay, you'll need:

- Python 3.9+
- A working installation of the M<>M Relay application
- Familiarity with Python and some experience with asynchronous programming (if you're new to these, this is a good way to learn!)
- A text editor or IDE (e.g., VS Code, PyCharm. I prefer [VSCodium](https://vscodium.com/))

## Understanding the Plugin System

Plugins in M<>M Relay are Python classes that extend what the relay can do. Think of them as mini-applications that can:

- Respond to commands from Meshtastic devices or Matrix rooms
- Store and retrieve data about nodes and users
- Send automated responses or notifications
- Process telemetry data and generate reports

All plugins inherit from a shared base class called `BasePlugin`, which provides the essential tools you need: logging, data storage, message handling, and configuration management.

### Structure of the Base Plugin

The `BasePlugin` is designed to provide a consistent interface for all plugins. Here's a quick look at some of its important features:

-   **Logging**: Each plugin has its own logger (`self.logger`) to help with tracking actions and debugging.
-   **Data Storage**: Methods like `store_node_data()`, `get_node_data()`, and `delete_node_data()` let plugins persistently store data specific to nodes.
-   **Message Handling**: Plugins can react to incoming messages from Meshtastic or Matrix by implementing specific methods.
-   **Configuration Options**: Plugins can access configuration options like `channels` from the `config.yaml` file. Note that the `plugin_response_delay` is now configured globally under the `meshtastic` section.

Each plugin must implement two key methods:

-   `handle_meshtastic_message(packet, formatted_message, longname, meshnet_name)`
-   `handle_room_message(room, event, full_message)`

These functions define how your plugin will handle incoming messages from Meshtastic nodes and Matrix rooms, respectively.

## Minimalist Plugin Example

Let's start with a simple example to get your feet wet. This plugin will respond to a specific command without handling channels or DMs, making it easier to understand the basics.

### Step 1: Set Up Your Plugin Repository

Plugins should be in their own project repositories. Create a new project repository on GitHub, GitLab, Codeberg, etc. Clone your new repo to your local machine and open it in your editor. Inside your cloned project, create a new file for the plugin.

_Note: For an easy start, you can fork this template repository [mmr-plugin-template](https://github.com/jeremiah-k/mmr-plugin-template) and add your own code._

For this example, create a file named `simple_responder.py` in your project repository.

### Step 2: Create the Plugin Class

Every plugin must inherit from `BasePlugin` and set its unique `plugin_name`. Here's the complete code for the `simple_responder` plugin:

```python
from mmrelay.plugins.base_plugin import BasePlugin
from mmrelay.meshtastic_utils import connect_meshtastic
from mmrelay.matrix_utils import bot_command

class Plugin(BasePlugin):
    plugin_name = "simple_responder"

    async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
        if "decoded" in packet and "text" in packet["decoded"]:
            message = packet["decoded"]["text"].strip()

            if message == "!hello":
                meshtastic_client = connect_meshtastic()

                # Respond with a greeting
                meshtastic_client.sendText(text="Hello from Meshtastic!", channelIndex=0)
                return True  # Indicate that we handled the message
        return False  # Indicate that we did not handle the message

    async def handle_room_message(self, room, event, full_message):
        if bot_command("hello", event):
            # Use the send_matrix_message method from BasePlugin
            # This method handles client initialization and error checking automatically
            await self.send_matrix_message(room.room_id, "Hello from Matrix!")
            return True  # Indicate that we handled the message
        return False  # Indicate that we did not handle the message
```

This example avoids complexities of channel and DM handling. It uses the `bot_command` function from `matrix_utils.py` to check if the message is a command directed at the bot, and `connect_meshtastic` from `meshtastic_utils.py` to send a message to the mesh network.

Notice how simple this is - just define `plugin_name` as a class variable and implement the two required methods. The `BasePlugin` handles all the initialization automatically.

### Step 3: Activate the Plugin in Your Configuration

If your plugin is hosted in a repository and you want the relay to clone it automatically, specify the repository and either a branch or tag:

> **Note**: The plugin system now supports separate `branch:` and `tag:` parameters. Use `branch:` for development branches and `tag:` for specific version tags. If both are specified, the tag will take precedence.

```yaml
community-plugins:
  simple_responder:
    active: true
    repository: https://github.com/YourUsername/SimpleResponder.git
    branch: main  # Use branch: for branches
    # OR
    # tag: v1.0.0  # Use tag: for specific version tags
```

### Step 4: Adding Dependencies

If your plugin requires additional Python packages, you can include a `requirements.txt` file in your repository. The plugin loader will automatically detect and install these dependencies when the plugin is loaded.

```
# Example requirements.txt
gpxpy==1.5.0
requests==2.28.1
```

The plugin system will automatically detect whether mmrelay is installed with pip or pipx and use the appropriate method to install dependencies:
- For pip installations: `pip install -r requirements.txt`
- For pipx installations: `pipx inject mmrelay <package>` for each package

### Step 5: Running and Testing the Plugin

After adding the plugin to your configuration, restart the relay. You should see a line in your log indicating that the **simple_responder** plugin has started:

```bash
DEBUG:Plugin:simple_responder:Started with priority=10
```

You can then send messages via Meshtastic or Matrix to verify that the plugin is working as expected.

## Creating a "Hello World" Plugin

Now, let's create a **HelloWorld** plugin. This plugin will simply log "Hello world" when it receives a message from either Meshtastic or Matrix, but won't send any responses.

Create a file named `hello_world.py` and add the following code:

```python
from mmrelay.plugins.base_plugin import BasePlugin

class Plugin(BasePlugin):
    plugin_name = "hello_world"

    async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
        self.logger.debug("Hello world, Meshtastic")
        return False  # Indicate that we did not handle the message

    async def handle_room_message(self, room, event, full_message):
        self.logger.debug("Hello world, Matrix")
        return False  # Indicate that we did not handle the message
```

Activate this plugin in your `config.yaml` just like you did with the `simple_responder` plugin.

## Legacy Plugin Support

If you're updating an existing plugin that uses the older initialization pattern, it will continue to work. The older pattern looks like this:

```python
class Plugin(BasePlugin):
    plugin_name = "my_plugin"

    def __init__(self):
        self.plugin_name = "my_plugin"  # Set before calling super()
        super().__init__()
```

However, for new plugins, the simplified approach shown in the examples above is recommended.

## Useful Functions from Other Modules

Besides the methods in `BasePlugin`, there are several functions in the relay's codebase you can use to avoid reinventing the wheel:

-   **Matrix Utilities**:
    -   `bot_command(command, event)`: A function from `matrix_utils.py` that helps determine if a Matrix message is a command directed at the bot.
    -   `send_matrix_message()`: Use this method from `BasePlugin` to send Matrix messages. It handles client initialization and error checking automatically.

     **Example Usage**:

    ```python
    from mmrelay.matrix_utils import bot_command

    async def handle_room_message(self, room, event, full_message):
        if bot_command("status", event):
            # Handle the 'status' command using the BasePlugin method
            await self.send_matrix_message(room.room_id, "System is running smoothly.")
            return True  # Indicate that we handled the message
        return False
    ```

    > ⚠️ **Important Note**: Never call `connect_matrix()` directly in your plugins. Always use the `send_matrix_message()` method provided by `BasePlugin`. This method handles client initialization, error checking, and prevents unnecessary credential reloading.

-   **Meshtastic Utilities**:
    -   `connect_meshtastic()`: A function from `meshtastic_utils.py` that returns the Meshtastic client interface. Useful for sending messages or accessing node information.

    ```python
    from mmrelay.meshtastic_utils import connect_meshtastic

    meshtastic_client = connect_meshtastic()
    ```

-   **Database Utilities**:
    -   For storing plugin-specific data, use the data persistence methods provided by `BasePlugin`. If you need to access node information like long names or short names, you can use functions from `db_utils.py`.

        -   `get_longname(meshtastic_id)`: Retrieve the longname for a given Meshtastic ID.
        -   `get_shortname(meshtastic_id)`: Retrieve the shortname for a given Meshtastic ID.

    ```python
    from mmrelay.db_utils import get_longname, get_shortname

    longname = get_longname(sender_id)
    shortname = get_shortname(sender_id)
    ```

## Advanced Topics

As you get more comfortable with plugin development, you can explore advanced features to enhance your plugins.

### Handling Direct Messages (DMs) and Channel-Specific Responses

Plugins can respond differently to DMs and messages in specific channels. This is controlled by the `channels` setting in your plugin's configuration.

#### Responding Only to Direct Messages (DMs)

To make your plugin respond **only** to DMs and ignore all channel messages, set the `channels` list to be empty:

```yaml
plugins:
  my_plugin:
    active: true
    channels: []  # Empty list: plugin only responds to DMs
```

In your plugin, check whether to respond based on the channel and whether it's a DM using `is_channel_enabled`:

```python
if not self.is_channel_enabled(channel, is_direct_message=is_direct_message):
    self.logger.debug(f"Channel {channel} not enabled for plugin '{self.plugin_name}'")
    return False
```

#### Responding to All Mapped Channels

To make your plugin respond to all mapped channels (defined in `matrix_rooms`), either omit the `channels` setting or comment it out:

```yaml
plugins:
  my_plugin:
    active: true
    # channels: [0,1,3,4]  # Commented out or omitted; plugin responds to all mapped channels
```

By default, if `channels` is not specified, the plugin responds to all mapped channels. The `is_channel_enabled` method handles this logic internally.

#### Responding to Specific Channels

To make your plugin respond only to specific channels, list them in the `channels` setting:

```yaml
plugins:
  my_plugin:
    active: true
    channels: [0, 1, 3, 4]  # List of Meshtastic channels to respond to
```

#### Handling Direct Messages (DMs)

By default, plugins always respond to DMs if active, regardless of `channels` configuration. The `is_channel_enabled` method in `BasePlugin` ensures DMs are always enabled:

```python
def is_channel_enabled(self, channel, is_direct_message=False):
    if is_direct_message:
        return True  # Always respond to DMs if the plugin is active
    else:
        return channel in self.channels
```

When processing a message, determine if it's a DM and pass `is_direct_message`:

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

This ensures your plugin responds appropriately to DMs and channel messages based on your configuration.

### Using Data Persistence

MMRelay provides two main methods for plugins to store data:

1. **Database Storage**: For structured data that needs to be queried or updated frequently
2. **File System Storage**: For larger files, binary data, or data that needs to be accessed by external tools

#### Database Storage

`BasePlugin` provides easy-to-use methods for saving and retrieving plugin-specific data in the SQLite database:

```python
# Store data for a specific node (appends to existing data)
self.store_node_data(meshtastic_id, {"temperature": 25.5, "timestamp": "2024-01-01"})

# Replace all data for a specific node
self.set_node_data(meshtastic_id, [{"temperature": 25.5}])

# Get data for a specific node
node_data = self.get_node_data(meshtastic_id)  # Returns a list

# Get all data for this plugin across all nodes
all_data = self.get_data()

# Delete data for a specific node
self.delete_node_data(meshtastic_id)
```

This is ideal for storing telemetry data, user preferences, or tracking information that you need to query later.

#### File System Storage

For larger files or binary data, MMRelay provides a standardized directory structure at `~/.mmrelay/data/plugins/<plugin_name>/`. The `BasePlugin` class provides a method to get this directory:

```python
# Get the plugin's data directory (~/.mmrelay/data/plugins/my_plugin/)
data_dir = self.get_plugin_data_dir()

# Get a subdirectory (creates it if it doesn't exist)
images_dir = self.get_plugin_data_dir('images')
logs_dir = self.get_plugin_data_dir('logs')
```

##### Example: Storing Files

```python
import os
from mmrelay.plugins.base_plugin import BasePlugin

class Plugin(BasePlugin):
    plugin_name = "my_plugin"

    def __init__(self):
        super().__init__()

        # Get the plugin's data directory
        self.data_dir = self.get_plugin_data_dir()

        # Or get a specific subdirectory
        self.images_dir = self.get_plugin_data_dir('images')

    def save_file(self, filename, content):
        file_path = os.path.join(self.data_dir, filename)
        with open(file_path, 'wb') as f:
            f.write(content)
```

##### Custom Data Directories

If your plugin needs to store data in a custom location (like a user-specified directory outside of `~/.mmrelay`), you can support this through configuration:

```python
# Check for custom directory in config
custom_dir = self.config.get('data_directory')
if custom_dir:
    # Expand user directory if needed (e.g., ~/my_data -> /home/user/my_data)
    custom_dir = os.path.expanduser(custom_dir)
    self.logger.info(f"Using custom data directory from config: {custom_dir}")
    self.data_dir = custom_dir
    # Ensure the directory exists
    os.makedirs(self.data_dir, exist_ok=True)
else:
    # Use the standardized plugin data directory
    self.data_dir = self.get_plugin_data_dir()
```

This allows users to specify their preferred data location in the plugin's configuration:

```yaml
plugins:
  my_plugin:
    active: true
    data_directory: ~/my_plugin_data
```

### Scheduling Background Tasks

If your plugin needs to perform periodic tasks, use the built-in scheduling capabilities from `BasePlugin`. The `start()` method lets you set up recurring background jobs:

```python
def start(self):
    schedule.every(5).minutes.do(self.background_job)
```

### Handling Meshtastic-Specific Commands

You can create specific commands for handling messages from the Meshtastic network. Here's a practical example that shows proper channel and DM handling:

```python
import asyncio
from mmrelay.meshtastic_utils import connect_meshtastic

async def handle_meshtastic_message(self, packet, formatted_message, longname, meshnet_name):
    if "decoded" in packet and "text" in packet["decoded"]:
        message = packet["decoded"]["text"].strip()
        channel = packet.get("channel", 0)

        meshtastic_client = connect_meshtastic()

        # Determine if the message is a direct message
        toId = packet.get("to")
        myId = meshtastic_client.myInfo.my_node_num
        is_direct_message = (toId == myId)

        # Check if this plugin should respond on this channel/DM
        if not self.is_channel_enabled(channel, is_direct_message=is_direct_message):
            return False

        if message.lower() == "!status":
            # Respect the response delay to avoid overwhelming the mesh
            await asyncio.sleep(self.get_response_delay())

            response = f"System status: OK. Nodes online: {len(meshtastic_client.nodes)}"
            fromId = packet.get("fromId")

            if is_direct_message:
                # Send DM response
                meshtastic_client.sendText(text=response, destinationId=fromId)
            else:
                # Send channel response
                meshtastic_client.sendText(text=response, channelIndex=channel)

            return True  # We handled this message

    return False  # We didn't handle this message
```

**Note on Response Delay**: If your plugin automatically responds to mesh commands, respect the `plugin_response_delay` configuration option. It's set globally under `meshtastic` in `config.yaml`. Retrieve it using `self.get_response_delay()` and apply it before sending your response, as shown above. This helps manage network traffic and prevents overwhelming the mesh network.

**Example `config.yaml` Setting:**

```yaml
meshtastic:
  plugin_response_delay: 5  # Delay in seconds before plugin responses; defaults to 3
```

## Appendix: `bot_command()` Implementation

The `bot_command` function is a helper utility located in `matrix_utils.py`. It's designed to figure out if an incoming Matrix message is a command directed at your bot. The reason the regex is so complicated is because it handles the various different ways Matrix clients format messages and mentions.

**Note: You don't need to modify this function.** It's already included in the `matrix_utils.py` file. You can simply import and use it in your plugins as shown in the examples.

For reference, here's the implementation of `bot_command()`:

```python
def bot_command(command, event):
    """
    Checks if the given command is directed at the bot,
    accounting for variations in different Matrix clients.
    """
    full_message = event.body.strip()
    content = event.source.get("content", {})
    formatted_body = content.get("formatted_body", "")

    # Remove HTML tags and extract the text content
    text_content = re.sub(r"<[^>]+>", "", formatted_body).strip()

    # Check if the message starts with bot_user_id or bot_user_name
    if full_message.startswith(bot_user_id) or text_content.startswith(bot_user_id):
        # Construct a regex pattern to match variations of bot mention and command
        pattern = rf"^(?:{re.escape(bot_user_id)}|{re.escape(bot_user_name)}|[#@].+?)[,:;]?\s*!{command}$"
        return bool(re.match(pattern, full_message)) or bool(re.match(pattern, text_content))
    elif full_message.startswith(bot_user_name) or text_content.startswith(bot_user_name):
        # Construct a regex pattern to match variations of bot mention and command
        pattern = rf"^(?:{re.escape(bot_user_id)}|{re.escape(bot_user_name)}|[#@].+?)[,:;]?\s*!{command}$"
        return bool(re.match(pattern, full_message)) or bool(re.match(pattern, text_content))
    else:
        return False
```

## Best Practices

1. **Use Logging Liberally**: Use `self.logger` to record key events, errors, or other relevant info for debugging and monitoring.
2. **Keep Plugins Modular**: Each plugin should handle a distinct piece of functionality for easier maintenance and debugging.
3. **Avoid Blocking Operations**: The relay is asynchronous, so be careful not to use blocking calls that could delay message handling.
4. **Respect Plugin Priorities**: Set plugin priorities appropriately by defining the `priority` attribute within your plugin class to control the order of message processing.
5. **Handle Response Delays**: If your plugin sends automatic responses, use `await asyncio.sleep(self.get_response_delay())` to respect the globally configured response delay.
6. **Manage Channel Responses**: Use `self.is_channel_enabled(channel, is_direct_message=is_direct_message)` to control where your plugin responds and ensure it handles DMs appropriately.
7. **Use BasePlugin Matrix Methods**: Always use the `send_matrix_message()` method from `BasePlugin` for sending Matrix messages. This method handles client initialization and error checking automatically. Never call `connect_matrix()` directly, as this will reinitialize the client and cause unnecessary credential reloading.
8. **Leverage Existing Functions**: Utilize functions from `matrix_utils.py`, `meshtastic_utils.py`, and `db_utils.py` to simplify your plugin code and maintain consistency.
9. **Use Standardized Data Storage**: Store plugin data in the standardized locations:
   - For structured data: Use the database methods (`store_node_data()`, `get_node_data()`, etc.)
   - For files and binary data: Use `self.get_plugin_data_dir()` to get the plugin's data directory

## Example Configuration

Here's a complete example of a community plugin configuration with all available options:

```yaml
community-plugins:
  gpxtracker:
    active: true
    repository: https://github.com/jeremiah-k/MMR-GPXTRacker.git
    tag: v1.0.0  # Use tag for specific versions
    # OR use branch for development:
    # branch: main

    # Plugin-specific configuration options:
    gpx_directory: "~/my_gpx_files"  # Custom directory for GPX files
    allowed_device_ids:
      - "*"  # Allow all devices
    priority: 50  # Lower numbers run first (default: 100)
```

For core plugins, the configuration is simpler:

```yaml
plugins:
  ping:
    active: true
    channels: [0, 1, 2]  # Specific channels, or omit for all mapped channels
    priority: 10
```

## Next Steps

Now that you know the basics, consider adding more complex features to your plugin. You could interact with external APIs, respond to specific commands, or handle more detailed data from the Meshtastic network. Check out existing plugins like `nodes_plugin.py` or `weather_plugin.py` for more ideas and examples.

If you have questions or run into issues, feel free to ask in the project's Matrix room [#mmrelay:meshnet.club](https://matrix.to/#/#mmrelay:meshnet.club).

Happy coding!
