# Sockets
Simple, easy to use, C/C++ single file socket wrapper with support on windows and linux for FTP, SSL, and UDP. 

![](https://img.shields.io/badge/Windows-passing-success)
![](https://img.shields.io/badge/Linux-passing-success)
![](https://img.shields.io/badge/Version-1.2-lightgray)


# Examples

### FTP / SSL Socket Client
```c
#include <stdio.h>
#include "socket.h"

int main(){
  SOCKET_t sock = createSock();
  errCode e;
  
  // errCode initSock(SOKCET_t sock)
  if((e=initSock(sock)) != no_err) throwError(e);
  
  // errCode connectSock(const char* hostname, int port, bool attemptSSL, SOCKET_t sock)
  if((e=connectSock("www.google.com", 443, true, sock)) != no_err) throwError(e);

  // void sendBytes(const char* bytes, int len, SOCKET_t sock)
  sendBytes("GET / HTTP/1.1\n\n", 17, sock);
  
  /*
   * int recvBytes(char buf[], int bufSize, int delay, SOCKET_t sock)
   * `delay`: how long in milliseconds to wait for a message (0 is infinite)
   * returns length of data sent to the client
  */
  char data[1024];
  recvBytes(data, 1024, 0, sock);

  puts(data); 
}
```

### FTP Server
```c
#include <stdio.h>
#include "socket.h"

// function to be ran on every connection
void onConn(SOCKET_t conn, void* arg){
    char data[1024] = {0};
    
    // int recvBytes(char buf[], int bufSize, int delay, SOCKET_t sock)
    int len = recvBytes(data, 1024, 0, conn);
    puts(data);

    sendBytes("HTTP/1.1 200 OK\n\nhi", 19, conn);
}

int main(){
    SOCKET_t sock = createSock();
    errCode e;

    // errCode initSock(SOKCET_t sock)
    if((e=initSock(sock)) != no_err) throwError(e);

    // errCode bindSock(const char* hostname, int port, SOCKET_t sock);
    if((e = bindSock("127.0.0.1", 8080, sock)) != no_err)
        throwError(e);

    /*
     * errCode listenSock(void (*onConn)(SOCKET_t conn, void* arg), int maxConn, SOCKET_t sock, void* arg)
     *
     * onConn: a function that is ran on each connection to the binded sock
     * maxConn: maximum ammount of connections
     * arg: a argument that is passed to the `onConn` func when a connection is made
    */
    if((e = listenSock(onConn, 10, sock, NULL)) != no_err)
        throwError(e);
}
```

### UDP Socket Client
```c
#include <stdio.h>
#include "socket.h"

int main(){
  SOCKET_t sock = createSock();
  errCode e;
  
  // errCode initSockUDP(SOKCET_t sock)
  if((e=initSockUDP(sock)) != no_err) throwError(e);

  // errCode sendBytesTo(const char* bytes, int len, const char* hostname, int port, SOCKET_t sock)
  if((e=sendBytesTo("Hello World!", 13, "127.0.0.1", 5005, sock) != no_err) throwError(e);
  
  /*
   * int recvBytes(char buf[], int bufSize, int delay, SOCKET_t sock)
   * `delay`: how long in milliseconds to wait for a message (0 is infinite)
   * returns length of data sent to the client
  */
  char data[1024];
  recvBytes(data, 1024, 0, udpSock);

  puts(data); 
}
```

# How to disable ssl
if you dont even want ssl to be attempted or dont want to link openssl you can do the following __before__ you include the header
```c
#define __NO_SSL__ // this removes the need for openssl to even be linked with the project, although ssl wont be even attempted

#include <stdio.h>
#include "socket.h"

int main(){
...
}
```
