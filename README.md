TauNet Protocol v0.1
================================
TauNet is a Raspberry Pi based distributed messaging network.

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
		* Only lowercase letters
		* 1 - 20 characters long
		* Must be unique
		* Must never change
	* ip
		* Must be IPv4
		* Must never change
	* port
		* Must never change

	This list is as static as it can possibly be.
	Users cannot be added or removed from a network.  (Save such features for
	future versions.)

	The protocol does not cover/allow for changing usernames, IP addresses,
	or ports.

[Here is an example network in JSON form.](https://github.com/JVMartin/cs300-protocol/blob/master/network.json)

Sending a Message
-----------------------
Populate your client program with the encryption key and the static list of
clients.

You are now ready to send a message to a user.

1. Establish a TCP connection to the desired user's IP address and port.

2. \[Edit me to do something involving specifically what CipherSaber
	preliminaries are needed.\]

3. Send a CipherSaber-encrypted string consisting of:

	* Your TauNet protocol version number
	* A pipe character
	* Your username
	* A pipe character
	* The intended recipient's username
	* A pipe character
	* The letter "m" (for "message")
	* A pipe character
	* A message of up to 500 characters

4. Disconnect.

Here is an example message prior to encryption:
```
v0.1|sally|robert|m|Hey Robert, how did you do on the exam?
```
