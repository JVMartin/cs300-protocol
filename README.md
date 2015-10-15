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
In order to create a network, compile the following two items.  Both items
are to be distributed to all clients in the client list.

1. **A network identifier/key.**

	This is a string of 32 random characters, generated using the Unix command:
	```BASH
	apg -a 1 -m 32 -M NCL -n 1
	```

	If apg is not installed on your Raspberry Pi, install it using:
	```
	sudo apt-get install apg
	```
	(See [man apg](http://linux.die.net/man/1/apg).)

	The chances of a string collision are astronomical.  Ensure that only
	yourself and the other clients have access to this key.

2. **A static list of clients.**

	This is a list of clients that can access the network.

	```
	+------------+-------------+------+--------------+
	|  username  |     ip      | port | last updated |
	+------------+-------------+------+--------------+
	| sally      | 11.11.11.11 | 11   | <timestamp>  |
	| james      | 22.22.22.22 | 22   | <timestamp>  |
	| robert     | 33.33.33.33 | 33   | <timestamp>  |
	| sarah      | 44.44.44.44 | 44   | <timestamp>  |
	| rupert     | 55.55.55.55 | 55   | <timestamp>  |
	| ...        | ...         | ...  | <timestamp>  |
	+------------+-------------+------+--------------+
	```

	* username
		* Only lowercase letters (no spaces, hyphens, etc.)
		* 3 - 12 characters long
		* Can never change
		* Each must be unique
	* ip
	* port
	* last updated
		* Indicates when IP address / port was last changed
		* Use elapsed time in seconds [since the Epoch](https://en.wikipedia.org/wiki/Unix_time): ```date +%s```
		* See [section on changing IP addresses](#changing-an-ip-address)

	This list is as static as it can possibly be.  Usernames cannot be changed.
	Users cannot be added or removed from a network.  (Save such features for
	future versions.)

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

If either has changed, save the current Epoch time (the time you discover
the change) to a variable.

Next, broadcast the change to all other clients on the network.

For each client on the client list:

1.  Establish a TLS connection to the client's IP address and port.

2.  Send a string:

	* The first 32 characters should be the network identifier.
	* The 33rd character should be an "i" (for "ip update").
	* The remaining characters should be (e.g. `sally|11.11.11.11|2222|1444892237`):
		* Your username
		* A pipe character
		* Your ip address
		* A pipe character
		* Your port
		* A pipe character
		* The time saved above

3.  Disconnect.

If you are a client *receiving* this string, update your client
table accordingly.

If you are a client *sending* this string, keep track of which clients
have received the message.  Keep attempting TLS connections with dead
clients every 5 seconds until all clients have been updated.
