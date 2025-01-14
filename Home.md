# Meshtastic <=> Matrix Relay

The Meshtastic <=> Matrix Relay is a powerful and easy-to-use tool that bridges Meshtastic devices and Matrix chat rooms, enabling seamless communication across platforms. This opens the door for connecting Meshtastic to [many other platforms](https://matrix.org/bridges/). This document covers:

1. Introduction to Meshtastic <=> Matrix Relay
2. Key Concepts
3. Use Cases

## 1. Introduction

The Meshtastic <=> Matrix Relay provides bidirectional communication between Meshtastic devices and Matrix chat rooms. This allows users on different mesh networks to communicate seamlessly. It also simplifies bridging Meshtastic to other platforms like Slack, Discord, and IRC through Matrix's extensive bridge network.

## 2. Key Concepts

### Mesh Networks

A mesh network is a decentralized network where each device can relay data. Messages can travel through multiple devices to reach their destination, unlike traditional networks that rely on a central server.

### Meshtastic

Meshtastic is an open-source, decentralized, and affordable mesh networking platform designed for secure, long-range communication. It offers various devices, including radio modules, that can communicate directly or relay messages through other devices in the network.

### Matrix

Matrix is an open standard for decentralized communication, providing a secure and interoperable messaging platform. It's a popular open-source alternative to Telegram or Discord, with a wide range of applications due to its open nature.

### Meshtastic <=> Matrix Relay

This software application relays messages between Meshtastic devices and Matrix chat rooms. It supports:

*   **Bidirectional communication**
*   **Multiple mesh networks**
*   **Serial and network connections**
*   **Message truncation** to fit Meshtastic's payload size
*   **Custom fields** for identifying the sender and mesh network in Matrix messages

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

## 3. Use Cases

### Remote Areas

The relay can provide communication in areas with limited or unreliable traditional infrastructure. Meshtastic devices form a local mesh network, which is then bridged to a Matrix chat room, allowing communication with the wider world.

### Just for Fun

Beyond practical uses, the relay adds a fun element to Meshtastic. Connect with anyone on Matrix or utilize Matrix bridges to reach users on other platforms, making Meshtastic a more versatile communication tool.
