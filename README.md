# BSL_Socket

**BSL_Socket** is a procedural TCP network socket library designed for the **Bonezegei Scripting Language (BSL)**. It provides raw socket control to create TCP clients, echo servers, and custom HTTP servers in `.bzg` scripts.

## Table of Contents
- [Installation](#installation)
- [Getting Started](#getting-started)
- [Code Examples](#code-examples)
  - [1. Simple TCP Server](#1-simple-tcp-server)
  - [2. HTTP Web Server](#2-http-web-server)
  - [3. TCP Client (HTTP Request)](#3-tcp-client-http-request)
- [API Reference](#api-reference)
- [License & Author](#license--author)
- [Citation](#citation)

## Installation

Install `BSL_Socket` using the BSL Package Manager (`bzg`):

```bash
bzg install socket
```


## Getting Started

To use the library in your script, include the socket module after installation:

```javascript
include("lib/socket.bzg");
```


## Code Examples

### 1. Simple TCP Server

This example sets up a procedural TCP socket server that listens on port `8080`, receives incoming data, and sends back a response.

```javascript
/*
    Socket Live Server (Procedural)
    Author: Jofel Batutay (Bonezegei)
    Date: April 17, 2026
*/

// include the library after Installation (" bzg install socket ")
include("lib/socket.bzg");

// 1. Init 
if (socket_init() < 0) {
    print("Socket Failed");
} else {
    print("Socket Ready");
}

// 2. Create
var server = socket_create(); 
if (server < 0) {
    print("Socket Creation Failed");
}

// 3. Bind Server will be at port 8080
if (socket_bind(server, 0, 8080) < 0) {
    print("Socket Bind failed.");
}

// 4. Listen
if (socket_listen(server, 5) < 0) {
    print("socket_listen() failed.");
}

while (1) {
    // 5. Accept
    var client = socket_accept(server);
    print("Client connected!\n");

    // 6. Read & Write
    var data = socket_read(client, 1024);
        
    if (sizeof(data) >= 0) {
        print("Received: " + data);

        var message = "Hello from BSL server";

        socket_write(client, message, sizeof(message));
        
        socket_close(client); //
        gc();   // manual garbage collection
    }
}    

socket_cleanup();
```

### 2. HTTP Web Server

A procedural HTTP server example returning a simple web page to any web browser connecting on port `8080`:

```javascript
/*
    HTTP Live Server (Procedural)
    Author: Jofel Batutay (Bonezegei)
    Date: April 17, 2026
*/

// include the library after Installation (" bzg install socket ")
include("lib/socket.bzg");

// 1. Init 
if (socket_init() < 0) {
    print("Socket Failed");
} else {
    print("Socket Ready");
}

// 2. Create
var server = socket_create(); 
if (server < 0) {
    print("Socket Creation Failed");
}

// 3. Bind Server will be at port 8080
if (socket_bind(server, 0, 8080) < 0) {
    print("Socket Bind failed.");
}

// 4. Listen
if (socket_listen(server, 5) < 0) {
    print("socket_listen() failed.");
}

while (1) {

    // 5. Accept
    var client = socket_accept(server);
    print("Client connected!\n");
    
    var data = socket_read(client, 1024);
        
    if (sizeof(data) >= 0) {
        print("Received: " + data);

        var BODY =   "<!DOCTYPE html>"                   
                    +"<html>"
                    +"<head><title>BSL Socket Server</title></head>"
                    +"<body>"
                    +"<h1>Hello from BSL HTTP Server </h1>"
                    +"<p>Your browser just spoke to a custom BSL_Socket  server.</p>"
                    +"</body>"
                    +"</html>";

        var HTTP =  "HTTP/1.1 200 OK\r\n"                
                    +"Content-Type: text/html\r\n"       
                    +"Content-Length: "+ sizeof(BODY)+"\r\n"    
                    +"Connection: close\r\n"
                    +"\r\n";                            

        socket_write(client, HTTP+BODY, sizeof(HTTP+BODY));

        print(HTTP + BODY);
        socket_close(client); //
        gc();   // manual garbage collection
    }
}    

socket_cleanup();
```

---

### 3. TCP Client (HTTP Request)

An example showing how to connect to an external socket or web server, send an HTTP `GET` payload, and print the output:

```javascript
/*
    Socket Client with (HTTP Request) (Procedural)
    Author: Jofel Batutay (Bonezegei)
    Date: April 17, 2026
*/

// include the library after Installation (" bzg install socket ")
include("lib/socket.bzg");

// 1. Initialize OS Socket Library
if (socket_init() < 0) {
    print("Failed to initialize sockets.");
}

// 2. Create the socket
var client_socket = socket_create();
if (client_socket < 0) {
    print("Failed to create socket.");
    return 1;
}

// 3. Connect to the server
if (socket_connect(client_socket, "127.0.0.1", 80) < 0) {
    print("Connection failed! Is the server running?");
    while (1) {}
} else {
    print("Connected successfully!\n");
}

// 4. Send a basic HTTP GET request to the server
var request = 
    "GET /index.html HTTP/1.1\r\n"
    +"Host: 127.0.0.1\r\n"
    +"Connection: close\r\n"
    +"\r\n"; // Blank line indicates end of request headers
        
socket_write(client_socket, request, sizeof(request));

// 5. Read the server's response
var data = socket_read(client_socket, 8192);
    
if (sizeof(data) > 0) {
    print(data);
} else {
    print("Server closed connection without sending data.\n");
}

// 6. Cleanup
socket_cleanup();
```
### 4 Live HTTP Server with paths

```javascript
include("lib/socket.bzg");
// 1. Init 
if (socket_init() <0){
    print("Socket Failed");
}
else{
    print("Socket Ready");
}

// 2. Create
var server = socket_create(); 
if(server < 0 ){
    print("Socket Creation Failed");
}

// 3. Bind Server will be at port 8080
if (socket_bind(server, 0, 8080) < 0) {
    print("Socket Bind failed.");
}

// 4. Listen
if (socket_listen(server, 5) < 0) {
    print("socket_listen() failed.");
}

while(1){

    // 5. Accept
    var client = socket_accept(server);
    print("Client connected!\n");
    
    var data = socket_read(client, 1024);
        
    if ( sizeof(data) >= 0) {
        //print("Received: " + data);
        var BODY =  "<!DOCTYPE html>"                   
                    +"<html>"
                    +"<head><title>BSL Socket Server</title></head>"
                    +"<body>"
                    +"<h1>Hello from BSL HTTP Server </h1>"
                    +"<p>Your browser just spoke to a custom BSL_Socket  server.</p>"
                    +"</body>"
                    +"</html>";

        var HTTP =  "HTTP/1.1 200 OK\r\n"                
                    +"Content-Type: text/html\r\n"       
                    +"Content-Length: "+ sizeof(BODY)+"\r\n"    
                    +"Connection: close\r\n"
                    +"\r\n";

        var match = regex("/.* HTTP/1.1", data);
        var path = substr(match, 0, sizeof(match) - 9);
        print("File Requested: " + path); 

        if (path == "/") {
            print("Serving index");
        }
        
        else if (path == "/about") {
            path[0] = " "; // remove leading slash
            print("Serving file: " + path);

            BODY = "<!DOCTYPE html><html lang=\"en\"><head><meta charset=\"UTF-8\"><meta name=\"viewport\" content=\"width=device-width,initial-scale=1.0\"><title>BSL HTTP Server</title><style>body{font-family:-apple-system,BlinkMacSystemFont,\"Segoe UI\",Roboto,Helvetica,Arial,sans-serif;display:flex;justify-content:center;align-items:center;height:100vh;margin:0;background-color:#f5f5f7;color:#1d1d1f}.container{text-align:center;padding:2rem;background:#ffffff;border-radius:12px;box-shadow:0 4px 6px rgba(0,0,0,0.05);max-width:400px}h1{font-size:1.5rem;margin-bottom:1rem;color:#0066cc}p{font-size:1rem;line-height:1.5;margin:0}</style></head><body><div class=\"container\"><h1>Hello from BSL HTTP Server</h1><p>Your browser just spoke to a custom BSL_Socket server.</p></div></body></html>";
            HTTP =  "HTTP/1.1 200 OK\r\n"                
                    +"Content-Type: text/html\r\n"       
                    +"Content-Length: "+ sizeof(BODY)+"\r\n"    
                    +"Connection: close\r\n"
                    +"\r\n";
        }
        else {
            print("File not found: " + path);
            BODY = "<h1>404 Not Found</h1>";
            HTTP =  "HTTP/1.1 404 Not Found\r\n"                
                    +"Content-Type: text/html\r\n"       
                    +"Content-Length: "+ sizeof(BODY)+"\r\n"    
                    +"Connection: close\r\n"
                    +"\r\n";
        }                          

        socket_write(client, HTTP+BODY, sizeof(HTTP+BODY));

        //print(HTTP + BODY);
        socket_close(client); //
        gc();   // manual garbage collection
    }
}    

socket_cleanup();
```



## API Reference

| Function Signature | Return Value | Description |
| :--- | :--- | :--- |
| `socket_init()` | `int` (`< 0` on error) | Initializes the underlying OS socket subsystem. |
| `socket_create()` | `int` (`< 0` on error) | Allocates and creates a new raw socket handle. |
| `socket_bind(server, addr, port)` | `int` (`< 0` on error) | Binds a server socket to a network address interface and port (e.g., `0` for default interface). |
| `socket_listen(server, backlog)` | `int` (`< 0` on error) | Sets the socket to listening state with a max backlog size for queued connections. |
| `socket_accept(server)` | `int` (Client Handle) | Blocks execution and accepts an incoming client connection handle. |
| `socket_connect(client, ip, port)`| `int` (`< 0` on error) | Connects a socket handle to a remote IP string and port integer. |
| `socket_read(socket, max_len)` | `string` | Reads incoming data up to `max_len` bytes from the target socket handle. |
| `socket_write(socket, data, len)` | `int` | Transmits `len` bytes of string/payload data over the socket. |
| `socket_close(socket)` | `void` | Closes an active client/server socket connection. |
| `socket_cleanup()` | `void` | Releases initialized OS socket subsystem resources. |

---

## License & Author

* **Author:** Jofel Batutay ([Bonezegei](https://github.com/bonezegei))
* **Website:** [bonezegei.com](https://bonezegei.com)

## Citation 
[![DOI](https://zenodo.org/badge/1213086179.svg)](https://doi.org/10.5281/zenodo.22163475)

If you use this library, please cite it as below:

**APA Format:**

Batutay, J. (2026). *bonezegei/BSL_Socket* [Computer software]. https://doi.org/10.5281/zenodo.22163475
