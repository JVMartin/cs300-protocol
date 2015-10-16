CS300 Raspberry Pi Protocol v0.1
================================
A protocol for our Raspberry Pi distributed messaging network.

Table of Contents
-----------------
 * [Creating a Network](#creating-a-network)
 * [Sending a Message](#sending-a-message)

Creating a Network
------------------
In order to create a network, compile the following two items.  Both items
are to be distributed to all clients in the client list.

1. **An encryption key.**

	Generate a secure encryption key.  All communications will be encrypted
	using the [RC4-based CipherSaber protocol](https://en.wikipedia.org/wiki/CipherSaber).

2. **A static list of clients.**

	This is a list of clients that can access the network.

	```
	+------------+-------------+------+
	|  username  |     ip      | port |
	+------------+-------------+------+
	| sally      | 11.11.11.11 | 11   |
	| james      | 22.22.22.22 | 22   |
	| robert     | 33.33.33.33 | 33   |
	| sarah      | 44.44.44.44 | 44   |
	| rupert     | 55.55.55.55 | 55   |
	| ...        | ...         | ...  |
	+------------+-------------+------+
	```

	* username
		* Only lowercase letters (no spaces, hyphens, etc.)
		* 3 - 12 characters long
		* Can never change
		* Each must be unique
	* ip
	* port

	This list is as static as it can possibly be.  Usernames cannot be changed.
	Users cannot be added or removed from a network.  (Save such features for
	future versions.)

	The protocol does not cover/allow for changing IP addresses or ports.

[Here is an example network in JSON form.](https://github.com/JVMartin/cs300-protocol/blob/master/network.json)

Sending a Message
-----------------------
Populate your client program with the encryption key and the static list of
clients.

You are now ready to send a message to a user.

1. Establish a TCP/IP connection to the desired user's IP address and port.

2. Send a CipherSaber-encrypted string consisting of:

	* Your username
	* A pipe character
	* The letter "m" (for "message")
	* A pipe character
	* A message of up to 500 characters

3. Disconnect.
