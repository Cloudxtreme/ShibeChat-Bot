ShibeChat-Bot Module
===================
This module can be used to make a chat bot for the website [ShibeChat](http://shibechat.com/) using Node.JS and it simplifies the process a great deal. Please note the following when making a bot for ShibeChat:

  * A bot account must have a username ending in bot.
  * Bot accounts must be registered as bots - contact a moderator on DogeChat to arrange this.
  * A bot must not make more than 2 sequential unprompted messages.

Installation
------------

To install the module and its dependencies, create a folder to make you bot in and open your command line there. Then type `npm install shibechat-bot` to install the module.

Getting Started
---------------

You will first need to sign up for a new account, following the rules above, on ShibeChat. Next you should create a room to host your bot in, this is usually the same as your bots name.
To start the script make a javascript file containing the following code and run this file to start your bot. Below is an example of a simple bot.

    var bot = require('shibechat-bot');
    bot.connect(BOT_USERNAME, BOT_PASSWORD, function() {
		bot.joinRoom("awesomebot");
		bot.chat("Bot is online!", "awesomebot");
	});
	
	bot.addCommand("!help", function(data) {
		bot.chat(data.user + ": Welcome to my bot! This message is displayed when a user types !help.", data.room);
	});

API
---

### bot.connect(username:String, password:String, fn:Function)
Connects to ShibeChat and logs in using the supplied credentials. Will callback on `fn` once successfully logged in.

    bot.connect("awesomebot", "mypassword", function() {
	    // Successfully logged in!
	});

### bot.addCommand(command:String, fn:Function(data))
This adds a command to the command registry so that when a user types `command` the function `fn` will be executed.

    bot.addCommand("!command", function(data) {
		// Handle what to do with the command here.
	});

`data` is an object containing some information about the message received.

  * `user` - The user who sent the message
  * `message` - A neatly formatted version of the message received with the command omitted
  * `msgArray` - An array containing the message split up by spaces to make handling parameters easier, with the command omitted
  * `command` - The command that has been executed, for example `!help`
  * `room` - The room in which the message was posted
  * `timestamp` - An ISO-8601 formatted datetime string of when the message was sent

### bot.chat(message:String, room:String)
This will send a chat message in the room `room` saying `message`.

    bot.chat("Hello, world!", "awesomebot");

### bot.PM(user:String, message:String)
This will send a private message to `user` saying `message`.
Note: This will send the message in the PM but your bot will not receive any replies that are sent to it unless you manually join the PM room.

    bot.PM("cainy", "Hey there!");

### bot.tip(user:String, amount:Integer, room:String[, message:String])
This will tip `user` the specified `amount` of dogecoins in the room `room` with the optional message: `message`.

    bot.tip("cainy", 20, "awesomebot", "Thanks for the cool node module!");

### bot.onChat(fn:Function(data))
This specifies a function to be called when the bot receives a chat message that is not recognised as a command.

    bot.onTip(function(data) {
		// Handle what to do when you receive a tip.
    });

`data` is an object containing some information about the message received.

  * `user` - The user who sent the message
  * `message` - A neatly formatted version of the message received
  * `msgArray` - An array containing the message split up by spaces to make handling parameters easier
  * `room` - The room in which the message was posted
  * `timestamp` - An ISO-8601 formatted datetime string of when the message was sent

### bot.onTip(fn:Function(data))
This specifies a function to be called when the bot receives a tip.

    bot.onTip(function(data) {
		// Handle what to do when you receive a tip.
    });

`data` is an object containing some information about the tip received.

  * `user` - The user who tipped you
  * `amount` - How many Dogecoins they tipped you
  * `message` - The message they specified (if any)
  * `room` - The room the user tipped you in
  * `timestamp` - An ISO-8601 formatted datetime string of when the message was sent

### bot.joinRoom(room:String)
This will send a request for your bot to join `room`.

    bot.joinRoom("awesomebot");

### bot.quitRoom(room:String)
This will quit the room `room`.

    bot.quitRoom("awesomebot");

### bot.getBalance()
This returns the bots balance. This is unfortunately often inaccurate because DogeChat does not always send you every balance update and because a bot is likely to send and receive many tips in quick succession this can become quite inaccurate. However, immediately after logging in it will be accurate.

    var myBalance = bot.getBalance();

### bot.setLogLevel(logLevel:Integer)
This sets how detailed the logging will be made by the module. This will default to `1` if not set.

  * `0` - No logs will be made at all.
  * `1` - Only important information and warnings will be logged
  * `2` - The above as well as debug logs
  * `3` - All of the above as well as any chat messages/tips received

### bot.logger(fn:Function(level, message))
This allows you to add your own custom handler for logging messages outputted by the module. These are by default logged to the console but if you want to write them to file or something you can use this method.
The example code below adds each log to a variable which could be later written to file, for example, in the same format that the module itslef logs them to the console.

    bot.logger(function(level, message, time) {
	    myLogs += "[" + level + "] " + message + "\n";
	});

  * `level` contains a string denoting what level of logging this is, it can be any of `DBUG`, `INFO`, `WARN` or `CHAT`
  * `message` contains the message which was logged
  * `time` is a JavaScript date object which is the time at which this message was logged

### bot.getSocket()
This will return the `socket.io-client` socket connection with ShibeChat for you to manually manage any other socket events.

License
--------

MIT License (c) 2014 Will Caine
