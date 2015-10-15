CS300 Raspberry Pi Protocol
===========================
A protocol for our Raspberry Pi distributed messaging network.

Table of Contents
-----------------
 * [Creating a Network](#creating-a-network)
 * [Connecting to a Network](#connecting-to-a-network)
 * [Changing an IP Address](#changing-an-ip-address)

Creating a Network
------------------
In order to create a network, compile the following two items:

1. **A network identifier/key.**

	This is a string of 32 random characters, generated using the Unix command:
	```BASH
	apg -a 1 -m 32 -M NCL -n 1
	```
	(See [man apg](http://linux.die.net/man/1/apg).)

	If apg is not installed on your Raspberry Pi, install it using:
	```
	sudo apt-get install apg
	```

	The chances of a string collision are astronomical.

	Ensure that only yourself and your clients have access to this key.

2. **A static list of clients.**

	This is a list of clients that can access the network.  Every client will
	have the network identifier that was generated above, and will also
	have this list of clients.

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

	This list is as static as it can possibly be.  Usernames cannot be changed.
	Users cannot be added or removed from a network.

	(Save such features for your own version 2.)

	Usernames must be composed only of lowercase letters (no spaces, hyphens, etc).

	[Changing IP addresses and ports](#changing-an-ip-address) are inevitable and
	will be dealt with below.

Connecting to a Network
-----------------------
Populate your program with the network identifier and the static list of
clients.

You are now ready to send a message to a user.

1. Establish a TLS connection to the desired user's IP address and port.

2. Send a string of at most 533 characters.

	* The first 32 characters should be the network identifier.
	* The 33rd character should be an "m" (for "message").
	* The remaining (up to 500) characters should be the message.

3. Disconnect.

Changing an IP Address
----------------------
Each time you start your client program, verify that your IP address and port
have not changed from the IP address and port representing you
in your client table.

If they have changed, broadcast the change to all other clients on the
network.

For each client on the client list:

1.  Establish a TLS connection to the client's IP address and port.

2.  Send a string:

	* The first 32 characters should be the network identifier.
	* The 33rd character should be an "i" (for "ip update").
	* The remaining characters should be your username, IP address, and port in the following format:
		```sally:11.11.11.11:2222```

3.  Disconnect.

If you are a client *receiving* this string, update your client
table accordingly.

If you are a client *sending* this string, keep track of which clients
have received the message.  Keep attempting TLS connections with dead
clients every 5 minutes until all clients have been updated.
