# Meshtastic <=> Matrix Relay

**Meshtastic <=> Matrix Relay** is a powerful and easy-to-use relay between Meshtastic devices and Matrix chat rooms, allowing seamless communication across platforms. This opens the door for bridging Meshtastic devices to many other platforms. In this wiki entry, we will cover the following topics:

- Introduction to Meshtastic <=> Matrix Relay
- Key concepts
- Use cases
- Advanced features

## 1. Introduction

The **Meshtastic <=> Matrix Relay** is a bidirectional message relay between Meshtastic devices and Matrix chat rooms. This allows communication between two or more mesh networks, providing seamless communication across platforms. This is especially useful for bridging Meshtastic devices to other platforms, such as Telegram, Discord, and IRC, among others, via Matrix bridges.

## 2. Key Concepts

### Mesh Networks

A mesh network is a decentralized network in which each node can relay data for the network. In a mesh network, messages can be routed through multiple nodes until they reach their destination. This is in contrast to traditional networks, in which messages are routed through a central server or gateway.

### Meshtastic

**Meshtastic** is an open-source, decentralized, and affordable mesh network platform designed for secure and long-range communication. It provides a range of devices, including radio modules, which can communicate with each other directly or through other devices in the network. Meshtastic devices can form a mesh network, allowing messages to be routed through multiple devices until they reach their destination.

### Matrix

**Matrix** is an open standard for decentralized communication. It is designed to provide a secure and decentralized messaging platform that is interoperable across different services and devices. It's often used as an open-source alternative to Telegram or Discord, but because of its very open nature, there is an incredibly wide range of applications that can be built on top of it.

### Meshtastic <=> Matrix Relay

The **Meshtastic <=> Matrix Relay** is a software application that allows communication between Meshtastic devices and Matrix chat rooms. It relays messages bidirectionally, supporting multiple mesh networks (meshnets). The relay supports serial, network, and Bluetooth connections for Meshtastic devices, and truncates long messages to fit within Meshtastic's payload size. The relay also includes custom keys in Matrix messages, which are used when relaying messages between two or more meshnets. It also has a plugin system that allows for extending functionality beyond just relaying messages.

#### Custom Keys in Matrix Messages

When a message is received from a remote meshnet, the relay includes the sender's longname, shortname, and the meshnet name as custom keys in the Matrix message. This metadata helps identify the source of the message and provides context for users in the Matrix chat room. The relay also supports relaying Matrix reactions when configured to do so.

**Example message format with custom keys:**

``json
{
  "msgtype": "m.text",
  "body": "[Alice/VM]: Hello from my very cool meshnet!",
  "meshtastic_longname": "Alice",
  "meshtastic_shortname": "AL",
  "meshtastic_meshnet": "VeryCoolMeshnet"
}
``

**Example Matrix reaction event:**

``json
{
  "type": "m.reaction",
  "sender": "@bob:example.com",
  "content": {
    "m.relates_to": {
      "rel_type": "m.annotation",
      "event_id": "$original_event_id",
      "key": "👍"
    }
  },
  "event_id": "$reaction_event_id",
  "origin_server_ts": 1680026472654,
  "room_id": "!abcdefg:example.com"
}
``

## 3. Use Cases

### Remote Areas

**Meshtastic <=> Matrix Relay** can be used to provide communication in remote areas. In areas where traditional communication channels are unavailable or unreliable, Meshtastic devices can form a mesh network, which can be bridged to a Matrix chat room using the Meshtastic <=> Matrix Relay. This allows people in remote areas to communicate with each other and with people in other parts of the world.

### Emergency Communications

The relay can be used to bridge communication between first responders using Meshtastic devices and emergency coordination centers using Matrix. This allows for real-time information sharing and coordination during emergencies.

### Community-Driven Networks

The relay can help create community-driven mesh networks connected to the wider internet through Matrix. This provides a resilient and decentralized communication infrastructure that is not dependent on traditional internet service providers.

### IoT and Sensor Networks

Meshtastic devices equipped with various sensors (e.g., temperature, humidity, GPS) can send data to the relay, which can then forward it to Matrix. This enables remote monitoring and data collection from IoT devices. The relay has a plugin architecture, allowing for developers to create their own plugins to handle this data in any way they see fit.

### Just for Fun?

Meshtastic is fun to use by itself, but with this utility, you're able to extend its reach much farther with the ability to talk to anyone on Matrix, or beyond using Matrix bridges.