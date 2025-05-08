# M<>M Relay

## (Meshtastic <=> Matrix Relay)

A powerful and easy-to-use relay between Meshtastic devices and Matrix chat rooms, allowing seamless communication across platforms. This opens the door for bridging Meshtastic devices to [many other platforms](https://matrix.org/bridges/).

## Installation

As of v1.0.0, M<>M Relay can be installed directly from PyPI:

```bash
# Install using pip
pip install mmrelay

# Or use pipx for isolated installation (recommended)
pipx install mmrelay
```

For detailed setup instructions, see the [Installation Guide](https://github.com/geoffwhittington/meshtastic-matrix-relay/blob/main/docs/INSTRUCTIONS.md).

If you're upgrading from a previous version, see the [Upgrade Guide](https://github.com/geoffwhittington/meshtastic-matrix-relay/blob/main/docs/UPGRADE_TO_V1.md).

## Documentation

- [Core Plugins](Core-Plugins.md)
- [Community Plugins](Community-Plugin-List.md)
- [Plugin Development](Community-Plugin-Development-Guide.md)
- [Getting Started with Matrix](Getting-Started-With-Matrix-&-MM-Relay.md)
- [Third-Party Projects](Third-Party-Projects.md)

## Key Concepts

### Mesh Networks

A mesh network is a decentralized network where each device can relay data. Messages can travel through multiple devices to reach their destination, unlike traditional networks that rely on a central server.

### Meshtastic

Meshtastic is an open-source, decentralized, and affordable mesh networking platform designed for secure, long-range communication. It offers various devices, including radio modules, that can communicate directly or relay messages through other devices in the network.

### Matrix

Matrix is an open standard for decentralized communication, providing a secure and interoperable messaging platform. It's a popular open-source alternative to Telegram or Discord, with a wide range of applications due to its open nature.

### M<>M Relay Features

This software application relays messages between Meshtastic devices and Matrix chat rooms. It supports:

- **Bidirectional communication**
- **Multiple mesh networks**
- **Serial, TCP, and BLE connections**
- **Message truncation** to fit Meshtastic's payload size
- **Custom fields** for identifying the sender and mesh network in Matrix messages
- **Plugin system** for extending functionality
- **Cross-platform reactions support**

### Custom Fields in Matrix Messages

When a message originates from a remote mesh network, the relay adds the sender's long name and mesh network name as custom fields to the Matrix message. This provides context about the message's origin.

Example message format with custom fields:

```json
{
"msgtype": "m.text",
"body": "[Alice/VeryCoolMeshnet]: Hello from my very cool meshnet!",
"meshtastic_longname": "Alice",
"meshtastic_meshnet": "VeryCoolMeshnet"
}
```

## Use Cases

### Remote Areas

The relay can provide communication in areas with limited or unreliable traditional infrastructure. Meshtastic devices form a local mesh network, which is then bridged to a Matrix chat room, allowing communication with the wider world.

### Just for Fun

Beyond practical uses, the relay adds a fun element to Meshtastic. Connect with anyone on Matrix or utilize Matrix bridges to reach users on other platforms, making Meshtastic a more versatile communication tool.

## Join the Community

- Project room: [#mmrelay:meshnet.club](https://matrix.to/#/#mmrelay:meshnet.club)
- Public relay room: [#relay-room:meshnet.club](https://matrix.to/#/#relay-room:meshnet.club)
