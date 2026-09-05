# Computer Networks — TCP & UDP Chat

A client-server chat application developed in **Java** to explore network communication using **TCP and UDP sockets**.

The project implements the same basic chat functionality using both transport protocols, including public messages, private messages, and file transfer.

## Overview

The application is divided into two independent implementations:

* **TCP** — connection-oriented communication using `Socket` and `ServerSocket`
* **UDP** — connectionless communication using `DatagramSocket` and `DatagramPacket`

Both implementations use a client-server architecture where a central server manages connected clients and routes messages between them.

## Features

* Client-server communication
* User nicknames
* Public chat messages
* Private messages
* Text file sharing
* File transfer
* Multiple clients connected simultaneously
* Separate TCP and UDP implementations
* Concurrent message receiving and sending in the TCP client

### Commands

Once connected, clients can use the following commands:

```text
/pm [nickname] [message]
```

Send a private message to another user.

```text
/sendtxt [nickname] [filename]
```

Send the contents of a text file to another user.

```text
/sendfile [nickname] [filename]
```

Transfer a file to another user.

```text
/exit
```

Leave the chat.

Any message that does not start with `/` is treated as a public message and broadcast to connected users.

## TCP Implementation

The TCP implementation uses Java's `Socket` and `ServerSocket` APIs.

The server listens on port `55555` and creates a dedicated thread for each connected client.

```text
             TCP
              │
              ▼
       ┌─────────────┐
       │   Server    │
       │   :55555    │
       └──────┬──────┘
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
    Client  Client  Client
```

The client uses separate threads for sending user input and receiving messages, allowing messages to be received while the user is typing.

### TCP File Transfer

Files are read from the local `tcp` directory and encoded using **Base64** before being sent through the socket.

Received files are stored in a user-specific inbox:

```text
tcp_inbox/
└── <nickname>/
    └── <filename>
```

## UDP Implementation

The UDP implementation uses Java's `DatagramSocket` and `DatagramPacket` APIs.

Unlike TCP, UDP does not establish a persistent connection between the client and server. Clients are identified by their IP address and port.

```text
             UDP
              │
              ▼
       ┌─────────────┐
       │   Server    │
       │   :55555    │
       └──────┬──────┘
              │
       ┌──────┼──────┐
       ▼      ▼      ▼
    Client  Client  Client
```

The server keeps track of active clients and their nicknames, then forwards incoming datagrams to the appropriate recipients.

### UDP File Transfer

Because UDP datagrams have a limited payload size, files are divided into smaller chunks before transmission.

The chunks are:

1. Read from the file
2. Encoded using Base64
3. Sent as individual UDP datagrams
4. Reassembled by the server
5. Written to the recipient's inbox

Received files are stored in:

```text
udp_inbox/
└── <nickname>/
    └── <filename>
```

## TCP vs UDP

The project provides a practical comparison between the two transport protocols.

|                 | TCP                        | UDP                                 |
| --------------- | -------------------------- | ----------------------------------- |
| Connection      | Connection-oriented        | Connectionless                      |
| Communication   | Stream-based               | Datagram-based                      |
| Reliability     | Provides reliable delivery | No delivery guarantee               |
| Ordering        | Maintains packet order     | No ordering guarantee               |
| Java API        | `Socket` / `ServerSocket`  | `DatagramSocket` / `DatagramPacket` |
| Client tracking | Socket connections         | IP address + port                   |
| File transfer   | Stream-based               | Chunk-based                         |

The two implementations expose the differences between TCP and UDP while providing similar application-level functionality.

## Project Structure

```text
LabRedes/
│
├── tcp/
│   ├── Client.java
│   ├── Server.java
│   ├── 1200.txt
│   ├── 2000.txt
│   └── testfileTCP.txt
│
└── udp/
    ├── Client.java
    └── Server.java
```

## Running the Project

### TCP

Start the server:

```bash
cd tcp
javac Server.java Client.java
java Server
```

Then, in another terminal, start one or more clients:

```bash
cd tcp
java Client
```

Choose a nickname when prompted.

### UDP

Start the UDP server:

```bash
cd udp
javac Server.java Client.java
java Server
```

Then start one or more clients:

```bash
cd udp
java Client
```

Choose a nickname when prompted.

> **Note:** The current implementation uses local loopback addresses (`127.0.0.5` and `127.0.0.6`) for the server, so the project is intended to be tested locally unless the addresses are changed.

## Technologies

* **Java**
* TCP/IP
* UDP
* Java Sockets
* Multithreading
* Client-server architecture
* Base64 encoding
* File I/O

## Academic Context

This project was developed as part of a **Computer Networks** course to explore transport-layer communication and the practical differences between TCP and UDP.

The implementation focuses on building the communication layer directly with Java's networking APIs rather than relying on higher-level networking frameworks.
