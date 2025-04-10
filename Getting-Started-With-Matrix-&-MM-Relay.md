# Getting Started With Matrix & M<>M Relay

This guide will help you get started with Matrix and M<>M Relay.

## Setting Up Matrix

1. **Install a Matrix Client**:
   - Download and install the [Element client](https://element.io/download) for your operating system
   - While a web client is available, we recommend using the standalone version for your main Matrix account

2. **Create a Matrix Account**:
   - Follow the instructions in Element to create a new account
   - You can use matrix.org as your homeserver, or choose another public homeserver

3. **Join the M<>M Relay Community**:
   - Follow [this link](https://matrix.to/#/#mmrelay:meshnet.club) to join our project room
   - Click "Continue" under the Element section
   - You'll be guided to the M<>M Relay project room where you can ask questions and get help

## Setting Up M<>M Relay

### Installation

As of v1.0.0, M<>M Relay can be installed directly from PyPI:

```bash
# Install using pip
pip install mmrelay

# Or use pipx for isolated installation (recommended)
pipx install mmrelay
```

### Configuration

1. Create a configuration file:
   ```bash
   # Create the standard config directory
   mkdir -p ~/.mmrelay
   
   # Copy the sample configuration
   cp sample_config.yaml ~/.mmrelay/config.yaml
   
   # Edit the configuration file
   nano ~/.mmrelay/config.yaml
   ```

2. Configure your Matrix credentials:
   ```yaml
   matrix:
     homeserver: "https://matrix.org"
     username: "your_username"
     password: "your_password"
     room_ids:
       - "#your-room:matrix.org"
   ```

3. Configure your Meshtastic connection:
   ```yaml
   meshtastic:
     connection:
       mode: "tcp"  # or "serial" or "ble"
       host: "meshtastic.local"  # for TCP mode
       # port: "/dev/ttyUSB0"  # for serial mode
   ```

### Running M<>M Relay

Start the relay with:

```bash
mmrelay
```

For more detailed instructions, see the [Installation Guide](https://github.com/geoffwhittington/meshtastic-matrix-relay/blob/main/INSTRUCTIONS.md).
