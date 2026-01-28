<div align="center">
  <img src="https://capsule-render.vercel.app/api?type=rect&color=gradient&customColorList=0,150,255&height=150&section=header&text=WebServ&fontSize=80&fontColor=ffffff&animation=fadeIn&fontAlignY=45&desc=A%20High-Performance%20HTTP/1.1%20Server%20in%20C++&descAlignY=75&descAlign=50&descSize=20&stroke=ffffff&strokeWidth=2"/>
  
  <br />
  
  <img src="https://img.shields.io/badge/Language-C++98-00599C?style=for-the-badge&logo=c%2B%2B&logoColor=white" />
  <img src="https://img.shields.io/badge/Architecture-Event_Driven-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Protocol-HTTP%2F1.1-green?style=for-the-badge" />
  <img src="https://img.shields.io/badge/School-1337-000000?style=for-the-badge&logo=42&logoColor=white" />
</div>

---

## 🌐 About The Project

**WebServ** is a fully functional HTTP server written in **C++98** from scratch. Unlike standard web servers (Apache/Nginx), this project does not use any external network libraries. It interacts directly with the OS kernel using **Sockets** and **Multiplexing**.

The goal was to build a robust server capable of handling multiple concurrent connections simultaneously without blocking, using a single thread. It strictly follows the **RFC 7230** protocol standards.



---

## ⚡ Technical Highlights

| Feature | Implementation Details |
| :--- | :--- |
| **I/O Multiplexing** | Uses `select()`, `poll()`, or `epoll()` to handle non-blocking I/O operations efficiently. |
| **HTTP Methods** | Full support for `GET` (Static files), `POST` (Uploads), and `DELETE`. |
| **CGI Support** | Executes external scripts (PHP, Python) via `fork()` and `execve()`, piping output back to the client. |
| **Robust Parsing** | State-machine based parsing for HTTP Headers, Chunked Transfer Encoding, and URI validation. |
| **Configuration** | Custom `.conf` file parser inspired by Nginx (Server blocks, Routes, Error pages). |
| **Stress Tested** | Validated using `Siege` for concurrency and memory stability (Zero Leaks). |

---

## 🏗️ Architecture: The Event Loop

The server operates on an **Event-Driven Architecture**:

1.  **Socket Creation:** Sets up a listening socket on the specified port.
2.  **Non-Blocking Mode:** All file descriptors (FDs) are set to non-blocking.
3.  **The Loop:**
    * **Monitor:** `epoll` waits for events (Read/Write) on FDs.
    * **Accept:** If the Listener has an event, accept new client.
    * **Read:** If a Client FD is readable, parse the raw HTTP request.
    * **Process:** Route the request (Static file or CGI).
    * **Write:** If Client FD is writable, send the built HTTP response.

```cpp
// Simplified Event Loop Logic
while (server_is_running) {
    int events = epoll_wait(epoll_fd, event_list, MAX_EVENTS, -1);
    for (int i = 0; i < events; i++) {
        if (event_list[i].data.fd == server_socket)
            handle_new_connection();
        else if (event_list[i].events & EPOLLIN)
            handle_request(event_list[i].data.fd);
        else if (event_list[i].events & EPOLLOUT)
            send_response(event_list[i].data.fd);
    }
}
```
🚀 Installation & Usage
Prerequisites
C++ Compiler (clang++ / g++)

Make

Building
```Bash

git clone https://github.com/70rn4d0/WebServer.git
cd WebServer_http
make
```
Running
```Bash

# Run with default config
./webserv first.conf
```
Testing (with Siege)
To test concurrency and availability:

```Bash

siege -b -c 100 -t 30S [http://127.0.0.1:8080/](http://127.0.0.1:8080/)
```
