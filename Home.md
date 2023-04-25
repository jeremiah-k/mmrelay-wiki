# Meshtastic <=> Matrix Relay

Meshtastic <=> Matrix Relay is a powerful and easy-to-use relay between Meshtastic devices and Matrix chat rooms, allowing seamless communication across platforms. This opens the door for bridging Meshtastic devices to many other platforms. In this wiki entry, we will cover the following topics:

1. Introduction to Meshtastic <=> Matrix Relay
2. Key concepts
3. Use cases
4. Advanced features

## 1. Introduction

The Meshtastic <=> Matrix Relay is a bidirectional message relay between Meshtastic devices and Matrix chat rooms. This allows communication between two or more mesh networks, providing seamless communication across platforms. This is especially useful for bridging Meshtastic devices to other platforms, such as Slack, Discord, and IRC, among others.

## 2. Key Concepts

### Mesh Networks

A mesh network is a decentralized network in which each node can relay data for the network. In a mesh network, messages can be routed through multiple nodes until they reach their destination. This is in contrast to traditional networks, in which messages are routed through a central server or gateway.

### Meshtastic

Meshtastic is an open-source, decentralized, and affordable mesh network platform designed for secure and long-range communication. It provides a range of devices, including radio modules, which can communicate with each other directly or through other devices in the network. Meshtastic devices can form a mesh network, allowing messages to be routed through multiple devices until they reach their destination.

### Matrix

Matrix is an open standard for decentralized communication. It is designed to provide a secure and decentralized messaging platform that is interoperable across different services and devices. Matrix is used by a wide range of platforms, including Riot, Element, and Jitsi.

### Meshtastic <=> Matrix Relay

The Meshtastic <=> Matrix Relay is a software application that allows communication between Meshtastic devices and Matrix chat rooms. It relays messages bidirectionally, supporting multiple meshnets. The relay supports both serial and network connections for Meshtastic devices and truncates long messages to fit within Meshtastic's payload size. The relay also includes custom keys in Matrix messages, which are used when relaying messages between two or more meshnets.

### Custom Keys in Matrix Messages

When a message is received from a remote meshnet, the relay includes the sender's longname and the meshnet name as custom keys in the Matrix message. This metadata helps identify the source of the message and provides context for users in the Matrix chat room.

Example message format with custom keys:
```
{
"msgtype": "m.text",
"body": "[Alice/VeryCoolMeshnet]: Hello from my very cool meshnet!",
"meshtastic_longname": "Alice",
"meshtastic_meshnet": "VeryCoolMeshnet"
}
```

## 3. Use Cases

### Remote Areas
Meshtastic <=> Matrix Relay can be used to provide communication in remote areas. In areas where traditional communication channels are unavailable or unreliable, Meshtastic devices can form a mesh network, which can be bridged to a Matrix chat room using the Meshtastic <=> Matrix Relay. This allows people in remote areas to communicate with each other and with people in other parts of the world.


### ~~Emergency Response~~ 

_Disclaimer: This is a hypothetical situation. Neither Matrix, nor Meshtastic should take the place of traditional emergency services. We do not recommend relying on these systems in an emergency._

~~Meshtastic <=> Matrix Relay can be used for emergency response communications. In situations where traditional communication channels are unavailable or unreliable, Meshtastic devices can be used to form a mesh network. The Meshtastic <=> Matrix Relay can then be used to bridge the mesh network to a Matrix chat room, allowing emergency responders to communicate with each other and coordinate their efforts.~~

_More to come..._
