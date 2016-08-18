# "*Comparing the Performance of Web Server Architectures*"

17 August, 2016 -- [link for paper](https://cs.uwaterloo.ca/~brecht/papers/getpaper.php?file=eurosys-2007.pdf)

## Abstract

In this paper, the authors compare three architecturally different server's performance, they are event-driven model, a thread-per-connection model, and a hybrid of events and threads model. From their observations, both event-based server and pipeline-based server provide better throughput than thread-based server.

## Pros

1. They implemented three architecturally different servers.

   |   Server    |               Architecture               |
   | :---------: | :--------------------------------------: |
   | $\mu$Server |               event-based                |
   |    Knot     | thread-based, highly-efficient Capriccio thread library |
   |   WatPipe   |      a hybrid of events and threads      |

   Authors tune all three servers as possible as they can to eliminate all other differences except architectural issues.

2. SYMPED (SYmmetric Multi-Process Event-Driven) architecture is introduced in this paper. It extends the SPED (Single Process Event-Driven) model by employing multiple processes each of which acts as a SPED server to mitigate blocking file I/O operations and to utilize processors. In this implementation, each process is a fully functional web server and independent to others, therefore, no synchronization or coordination is required.

3. Knot server is based on efficient Capriccio thread library. As they mentioned, Capriccio provides a scalable, cooperatively scheduled, and user-level threading package for use with high concurrency servers. It represents the state-of-the-art in Linux threading package. Also, authors implements non-blocking socket methods in Capriccio.

4. WatPipe server is similar to SEDA (Staged Event-Driven Architecture). The stages in WatPipe are self-contained and linked using queues. Separate thread pools or shared thread pools are used among different stages.

5. Software configurations tunning is important for server to get higher throughput. They includes the size of thread pool, maxium connection number and worker threads number (if it has one). In addition, zero-copy `sendfile` is emphasized in this paper, it is used to mitigate the overhead of copying among kernel space and user space.

## Cons

1. For all experiments the authors run are in a single processor environment, they claim because Capriccio does not support multiple processors. Therefore, the scenario of *context switch* among different processors may not happen in their experiments. 

2. I am curious about how Capriccio thread library works.  For Capriccio threading library, it is described in paper "*Why Events Are A Bad Idea (for high-concurrency servers)*".

   > Our thread package uses the `coro` coroutine library for minimalist context switching, and it translates blocking I/O requests to asynchronous requests internally. For asynchronous socket I/O, we use the UNIX `poll()` system call, whereas asynchronous disk I/O is provided by a thread pool that performs blocking I/O operations.

   Therefore, it seems the library itself also uses event-mechanism to handle asynchronous sockets internally. It means the main overhead of event-driven architecture also has a change to influence the performance of server based on Capriccio thread library.

## Personal Summary

This paper compares throughputs of three architecturally difference web servers under a certain workload. It claims an event-based $\mu$Server and a pipeline based WatPipe can achieve better performance than an efficient thread library based Knot. However, it lacks of detailed overhead analysis of three architecturally different web servers. 