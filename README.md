> This repository has opted-in to Hacktoberfest!

# 🔌 RLP
*Forked from ReAdminRBX/roblox-long-polling*

RLP is a module for Node.js and Roblox that allows you to get the efficiency and ease-of-use of websockets in Roblox, using a technology called long-polling that keeps an HTTP connection open for as long as possible.

## Installation

Use the package manager [npm](https://npmjs.com) to install the RLP server.

```bash
npm install @grid-rbx/rlp
```
And download the Roblox module [here](https://github.com/grid-rbx/rlp/blob/master/LongPolling.rbxm).
## Usage

 1. Install both modules (See above)
 2. Ensure HTTPService is enabled on Roblox
 3. Add the server module to your Node.js project, and use the example code below
 4. Set up the server module with your configuration
```js
    const rlp = require("@grid-rbx/rlp");

    const poll = new rlp({
	    port: 2004, // Add this behind your IP, e.g. http://127.0.0.1:2004,
	    // password: "<password>", 
        // If you want to add a simple password, uncomment the line above and insert a password into the string.
        redisConnection: {
            host: "<redis_address>",
            port: 6379
        }
    });
    
    poll.on('connection', (connection) => {
        console.log('New connection', connection.id); // Will fire when a new connection is active, and include the IP address.
        poll.broadcast("new connection", connection.id); // Will broadcast to all active sockets that this one has joined the party.
    
        connection.send('greeting', 'Hi!'); // Will send a welcome message to the new socket.

        connection.on('returned_greeting', (data) => { // Handle the returned greeting.
            console.log("Received return greeting:", data);
        });
    
        connection.on('internal_ping', () => { // We receive pings from the server to let us know its still alive. 
            console.log("Keep-Alive Ping received");
        });
    
        connection.on('disconnect', () => { // Fired when the game sends a disconnect command, or our timeout is fired.
            console.log('Disconnection', connection.id);
            poll.broadcast("disconnection", connection.id);
        });
    });
```
 1. Create a script in ServerScriptService that requires the client module we installed earlier, this is our magic code that allows you to interface with the server code.
```lua
local rlp = require(path.to.module)

local connection = rlp("http://<address>:<port>", "")


connection:on("greeting", function(message) -- This is an event fired in the above example, you can change this if you want into your own events.
    print("Recieved greeting: ", message)
end)

connection:on("new connection", function(id) -- This is an event fired in the above example, you can change this if you want into your own events.
    print("New Connection: ", id)
end)

connection:on("disconnection", function(id) -- Fired if we for some reason get disconnected.
    print("Disconnection: ", id)
end)
connection:send("return_greeting", "Hello!") -- Example on how to send messages.

task.wait(30)
connection:Disconnect()
```

## Contributing
Pull requests are welcome. For major changes, please open an issue first to discuss what you would like to change.

## License
This project uses the MIT License, which you can learn more about [here](https://choosealicense.com/licenses/mit/)
