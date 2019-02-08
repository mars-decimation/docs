Mars Decimation Protocol
======

To start a new connection to the server, the client first connects to the server with an SSL socket.
The default port for this connection is 3411 (as of this time).
Directly after connecting to the server, the client starts the Login process.

All integers are transferred in little endian.

Login
------

Right after the connection to the server is opened, the server sends a status packet to the client.
After that, either a login packet, a registration packet, a forgotten password packet, or a password recovery packet must be sent from the client to the server.

### Server Status

The server status contains ASCII line-based text in the following format:

```
Product Name (Version)
Server Name
Server Status
```

An example of this packet may look like this:

```
Mars Decimation (1.0.0 commit abcdef)
Official Server
OK
```

The server status may be any string, but the following statuses are implemented by the official implementation:

* OK

### Login Packet

The login packet has the following format:

Offset | Size | Description
-------|------|------------
0      | 1    | Packet ID (0x00)
1      | 1    | Length of username (n)
2      | 2    | Length of password (m)
4      | n    | Username
4 + n  | m    | Password

The response to this packet is a single byte denoting a status.
The valid status codes are as follows:

* 0x00: Login Success
* 0x01: Invalid Credentials
* 0x02: Unverified Account

In the case of a successful login, the client is then joined to a game.
Otherwise, the connection stays in the login state and another login packet, or any of the packets in this section, can be sent to the server.

### Registration Packet

The registration packet has the following format:

Offset | Size | Description
-------|------|------------
0      | 1    | Packet ID (0x01)
1      | 1    | Length of username (n)
2      | 2    | Length of email (m)
4      | 2    | Length of password (o)
6      | n    | Username
6 + n  | m    | Email
6 + n + m | o | Password

The response to this packet is a single byte denoting a status.
The valid status codes are as follows:

* 0x00: Success
* 0x80: Email Already Taken
* 0x81: Username Already Taken
* 0x82: Insecure Password

After a successful login, the user must verify his/her email before loggin in, so the connection will stay in the login state.

### Forgotten Password Packet

The forgotten password packet has either of the following formats:

Offset | Size | Description
-------|------|------------
0      | 1    | Packet ID (0x02)
1      | 2    | Length of email (n)
3      | n    | Email

Offset | Size | Description
-------|------|------------
0      | 1    | Packet ID (0x03)
1      | 1    | Length of username (n)
2      | n    | Username

There is no response given to this packet.

### Password Recovery Packet

The password recovery packet has the following format:

Offset | Size | Description
-------|------|------------
0      | 1    | Packet ID (0x04)
1      | 2    | Length of password (n)
3      | 16   | Recovery code in binary form
19     | n    | New password

The response to this packet is a single byte denoting a status.
The valid status codes are as follows:

* 0x00: Success
* 0x01: Invalid Credentials
* 0x02: Unverified Account
* 0x82: Insecure Password

In the case of a successful recovery, the client is then joined to a game.
Otherwise, the connection stays in the login state and another login packet, or any of the packets in this section, can be sent to the server.
