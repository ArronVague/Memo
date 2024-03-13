##### 实现即时通信用的什么协议？http吗？

WebSocket（[深入理解WebSocket协议](#深入理解WebSocket协议)）。服务端与每一个客户端建立长连接后，为每个客户端开启两个协程，一个接收一个发送。

##### 粘包现象是什么？怎么处理？

"粘包"是网络编程中一个常见的问题，主要发生在基于TCP/IP协议的数据传输中。TCP/IP是一个面向流的协议，数据在传输过程中，可能会将多个数据包合并成一个数据包进行发送，或者将一个大的数据包拆分成多个小的数据包进行发送，这就导致了接收端在接收数据时，可能会一次接收到多个数据包，或者需要多次接收才能完全获取一个数据包，这种现象就称为"粘包"。

处理粘包的常见方法有以下几种：

1. **消息边界定义**：在每个消息的开始或结束处添加特殊的标识符，用于标识消息的边界。这种方法简单易实现，但需要确保这些标识符在消息内容中不会出现，否则可能会导致误判。

2. **固定长度的消息**：如果所有的消息都是固定长度的，那么接收端可以根据这个长度来接收数据，从而避免粘包问题。但这种方法的缺点是如果消息的长度变化很大，可能会导致网络的利用率不高。

3. **长度前缀**：在每个消息的开始处添加一个表示消息长度的字段，接收端首先读取这个字段，然后根据这个长度来读取剩余的消息。这种方法可以处理任意长度的消息，但需要在发送和接收端都实现相应的逻辑来处理这个长度字段。

4. **使用特定的协议**：一些协议，如HTTP和WebSocket，已经在协议层面解决了粘包问题。如果你正在使用这些协议，那么你不需要自己处理粘包问题。

##### 第3种方法——长度前缀中，接收方怎么知道长度，哪些字段可以知道？

消息是以字节流的形式传输的。在使用长度前缀的方法时，通常约定前几个字节（例如前4个字节）用来表示消息的长度。

在接收端，首先读取这些长度字段的字节，然后将这些字节转换为整数，这个整数就是接下来的消息长度。然后，再读取这么多字节的数据，这些数据就是消息。

这里是一个简单的示例（假设长度字段是4字节，网络字节序是大端）：

```python
import socket
import struct

# 创建一个socket对象
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 连接到服务器
s.connect(('localhost', 12345))

# 读取4个字节的长度字段
length_bytes = s.recv(4)

# 将字节转换为整数
length = struct.unpack('>I', length_bytes)[0]

# 读取这么多字节的数据
data = s.recv(length)

# 这就是你的消息
message = data.decode('utf-8')
```

需要注意的是，这个示例假设了所有的消息都能一次性完整地接收到。在实际的网络编程中，可能需要处理网络延迟和丢包等问题，可能需要多次接收才能获取完整的消息。这就需要在代码中添加适当的错误处理和循环逻辑。

##### UDP会粘包吗？

不会，UDP（User Datagram Protocol，用户数据报协议）不会出现粘包问题。

这是因为UDP是一种无连接的、面向数据报的协议。每个UDP数据报都是独立的，每个数据报包含完整的源地址和目标地址信息，因此，每个数据报都可以独立路由和传输，不依赖于其他数据报。在接收端，每次接收到的都是完整的UDP数据报，不会出现一个数据报被拆分成多个部分，或者多个数据报被合并成一个数据报的情况，因此，UDP不会出现粘包问题。

然而，这并不意味着使用UDP就没有任何问题。由于UDP是无连接的，它不保证数据报的到达，也不保证数据报的顺序。如果需要这些保证，那么就需要在应用层实现相应的逻辑，或者使用其他的协议，如TCP。

##### UDP传输的最大长度？

根据 UDP 协议，从 UDP 数据包的包头可以看出，UDP 的最大包长度是 2^16-1 个字节。

由于 UDP 包头占 8 个字节，而在 IP 层进行封装后的 IP 包头占去 20 字节，所以这个是 UDP 数据包的最大理论长度是 2^16 - 1 - 8 - 20 = 65507 字节。

补充：UDP理想数据包长度

每个以太网帧都有最小的大小 64 字节，最大不能超过 1518 字节。

除去链路层的首部和尾部的 18 个字节，链路层的数据区范围是 46-1500 字节，

那么链路层的数据区，即 MTU(最大传输单元)为 1500 字节。

事实上这个 1500 字节就是网络层 IP 数据报的长度限制。

因为 IP 数据报的首部为 20 字节，所以 IP 数据报的数据区长度最大为 1480 字节。而这个 1480 字节就是用来放 TCP 传来的 TCP 报文段或 UDP 传来的 UDP 数据报的。

除去 UDP 包头占 8 个 字节，那么 UDP 数据报的数据区最大长度为 1472 字节。

[UDP vs TCP](#UDP vs TCP)

##### 进程和线程的区别？

当然，以下是一个更新后的比较表格：

| 特性       | 进程                                                         | 线程                                                         |
| ---------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 定义       | 进程是操作系统进行资源分配和调度的基本单位，是一个运行中的程序的实例。 | 线程是进程中的执行单元，是操作系统进行调度的基本单位。       |
| 内存空间   | 每个进程有自己的独立内存空间，进程间不共享。                 | 线程共享所属进程的内存空间，包括全局变量，堆内存等。         |
| 创建和销毁 | 创建和销毁进程的开销比较大。                                 | 创建和销毁线程的开销相对较小。                               |
| 通信       | 进程间通信需要通过操作系统提供的IPC（进程间通信）机制，如管道、消息队列、信号量、共享内存等。 | 线程间可以直接读写所属进程的共享数据，通信开销小。           |
| 调度       | 进程上下文切换的开销大。                                     | 线程上下文切换的开销小。                                     |
| 数据保护   | 进程间数据相互独立，一进程崩溃不会影响其他进程。             | 一个线程崩溃可能会导致整个进程崩溃。                         |
| 共享内存   | 进程间可以通过共享内存的方式进行通信，但需要特别的编程技巧来管理并防止冲突。 | 线程间自然共享内存，可以直接访问和修改共享数据，但需要处理同步和互斥问题。 |

[协程、线程、进程的区别](https://github.com/ArronVague/Memo/issues/5)

##### 线程之间怎么做到安全？

线程之间共享同一进程的内存空间，这意味着多个线程可能会试图同时访问和修改同一块内存区域。如果不加以控制，这可能会导致数据不一致和其他问题，这被称为"竞态条件"。为了避免这种情况，需要使用一些同步原语（synchronization primitives）来确保线程间的安全：

1. **互斥锁（Mutex）**：互斥锁是一种保护资源的方法，只允许一个线程在同一时间访问特定的代码段或资源。如果一个线程已经"锁定"了一个互斥锁，其他试图锁定同一互斥锁的线程将被阻塞，直到第一个线程释放锁。

2. **信号量（Semaphore）**：信号量是一种更通用的同步机制，可以控制同时访问特定资源或代码段的线程数量。信号量有一个值，表示可以同时访问的线程数量。当一个线程试图"获取"信号量时，信号量的值会减一；当线程"释放"信号量时，信号量的值会加一。如果信号量的值为零，试图获取它的线程将被阻塞，直到其他线程释放信号量。

3. **条件变量（Condition Variables）**：条件变量用于等待某个特定条件的满足。一个线程可以在条件变量上等待，直到其他线程改变了某个条件并通知等待的线程。

4. **读写锁（Read-Write Locks）**：读写锁允许多个线程同时读取某个资源，但在任何时候只允许一个线程写入。这可以提高在读操作远多于写操作的情况下的性能。

使用这些同步原语需要谨慎，因为不正确的使用可能会导致死锁（两个或更多线程永久地等待对方释放资源）、饥饿（某些线程无法获得所需的资源）和其他问题。

##### 了解select和epoll吗，epoll的底层是什么？

`epoll` 是 Linux 提供的一种 I/O 多路复用技术，它在内核级别实现，以提供高效的事件通知机制。`epoll` 使用一种称为红黑树的数据结构来跟踪注册的文件描述符。当一个事件发生时，内核会将相关的文件描述符添加到一个就绪列表中，然后通知应用程序。

以下是 `epoll` 的基本工作流程：

1. 应用程序调用 `epoll_create` 创建一个 `epoll` 对象，这将返回一个文件描述符。

2. 应用程序使用 `epoll_ctl` 注册要监视的文件描述符，并指定感兴趣的事件（如读、写、连接关闭等）。

3. 在事件发生时，内核会更新就绪列表。

4. 应用程序调用 `epoll_wait` 获取就绪的事件。如果没有事件就绪，`epoll_wait` 可以阻塞直到有事件发生，或者在指定的超时时间后返回。

这种设计使得 `epoll` 比传统的 `select` 和 `poll` 系统调用更加高效，特别是在处理大量并发连接时。`select` 和 `poll` 需要遍历所有注册的文件描述符，而 `epoll` 只需要处理那些已经就绪的事件，因此其性能并不会随着文件描述符数量的增加而线性下降。

[I/O多路复用](https://github.com/ArronVague/Memo/issues/6)

##### 红黑树和B+树有什么区别？B+树的应用场景有什么？

红黑树和B+树都是自平衡的搜索树，但它们有一些主要的区别，并且因此在不同的应用场景中有不同的优势。

**红黑树**：

1. 红黑树是一种自平衡的二叉搜索树，每个节点都带有颜色属性，是红色或黑色。
2. 红黑树的主要优势在于插入、删除和查找操作的时间复杂度都是O(log n)。
3. 红黑树通常用于实现关联数组和动态排序数据结构，如C++ STL中的map和set。

**B+树**：

1. B+树是一种多路搜索树，每个节点可以有多于两个的子节点。
2. B+树的所有值都在叶子节点，非叶子节点仅用来做索引，这使得在查找时有更好的磁盘读性能（因为磁盘读取是块级的）。
3. B+树的每个叶子节点都通过指针连接，这对于范围查询非常有利。
4. B+树通常用于数据库索引和文件系统。

**B+树的应用场景**：

由于B+树的特性，它们在以下场景中被广泛应用：

1. **数据库和文件系统**：B+树是大多数数据库（如MySQL，Oracle等）和文件系统（如NTFS，ext等）中使用的主要数据结构。B+树的结构使得磁盘的读写操作更加高效，因为它可以减少磁盘I/O操作。

2. **范围查询**：B+树的所有叶子节点都通过指针连接，这使得范围查询非常快速，这在数据库查询中非常有用。

3. **大量数据的存储和检索**：B+树可以存储大量数据，而且检索效率高，因此它非常适合用于大数据的存储和检索。

总的来说，红黑树和B+树都是非常高效的数据结构，但根据它们的特性，它们在不同的场景中有不同的应用。

##### MySQL的底层实现？

MySQL使用了多种数据结构来实现其功能，其中最重要的是B+树。B+树是一种自平衡的树，适用于大量数据的存储系统，如文件系统和数据库。

##### MySQL的索引？（不是指应用层面的索引）

在MySQL中，最常用的存储引擎InnoDB就使用了B+树作为其主要的索引结构。在InnoDB中，表都是根据主键在B+树中进行存储的，这种树被称为聚簇索引。对于非主键的索引（称为二级索引或非聚簇索引），它们的叶节点并不包含行的全部数据，而是包含了对应行的主键值。当通过非聚簇索引查询数据时，InnoDB会先在B+树中查找到主键，然后再通过主键在聚簇索引中查找到完整的行数据。

[什么是聚簇索引和非聚簇索引](#什么是聚簇索引和非聚簇索引)

此外，其他的MySQL存储引擎，如MyISAM，也使用了B+树作为其索引结构，但是其实现方式和InnoDB有所不同。例如，MyISAM的索引并不包含行的全部数据，而是包含了行数据的物理地址。

##### 题目

###### 链表，奇节点全放前面，偶节点全放后面（https://leetcode.cn/problems/odd-even-linked-list/description/）

双指针

###### 第k大的数，几种做法？时间复杂度呢？（https://leetcode.cn/problems/kth-largest-element-in-an-array/description/）

1. 排序 O(nlogn + k)
2. 堆排序（大根堆） O(n + klogn)
3. 维护大小为k的小根堆，堆顶即为答案 O(nlogk)

###### 斐波那契数列？（https://leetcode.cn/problems/fibonacci-number/description/）

1. 递归
2. 动态规划

---

##### 深入理解WebSocket协议

| 特性     | WebSocket        | HTTP                         |
| -------- | ---------------- | ---------------------------- |
| 通信方式 | 全双工、双向通信 | 请求-响应模式，半双工通信    |
| 协议类型 | 使用自定义协议   | 标准化的协议                 |
| 数据传输 | 发送非常少量数据 | 字节级开销，可能发送大量数据 |

WebSocket复用了http的握手通道。ajax就是在这样的通道上完成数据传输的，只不过ajax交互是短连接，在一次 request->response 之后，“通道”连接就断开了。

**在这个握手的过程当中，客户端和服务端主要做了两件事情：**

- 1）建立了一条连接“握手通道”用于通信（这点和HTTP协议相同，不同的是HTTP协议完成数据交互后就释放了这条握手通道，这就是所谓的“短连接“，它的生命周期是一次数据交互的时间，通常是毫秒级别的）；
- 2）将HTTP协议升级到WebSocket协议，并复用HTTP协议的握手通道，从而建立一条持久连接。

注意：维持“长连接”需要消耗服务器资源。

HTTP协议是基于TCP实现的，HTTP发送数据也是分包转发的，就是将大数据根据报文形式分割成一小块一小块发送到服务端，服务端接收到客户端发送的报文后，再将小块的数据拼接组装。WebSocket协议也是通过分片打包数据进行转发的，不过策略上和HTTP的分包不一样。

WebSocket通信中，客户端发送数据分片是有序的，这一点和HTTP不一样，HTTP将消息分包之后，是并发无序的发送给服务端的，包信息在数据中的位置则在HTTP报文中存储，而WebSocket仅仅需要一个FIN比特位就能保证将数据完整的发送到服务端。

WebSocket长连接浪费服务端资源。但是，举个例子：你几个月没有和一个QQ好友聊天了，突然有一天他发QQ消息告诉你他要结婚了，你还是能在第一时间收到。那是因为，客户端和服务端一直再采用心跳来检查连接。

[实现即时通信用的什么协议？http吗？](#实现即时通信用的什么协议？http吗？)

---

##### UDP vs TCP

| 特性         | UDP                                              | TCP                                        |
| ------------ | ------------------------------------------------ | ------------------------------------------ |
| 连接         | 无连接                                           | 面向连接                                   |
| 数据传输     | 数据报                                           | 数据流                                     |
| 传输可靠性   | 不可靠                                           | 可靠                                       |
| 顺序         | 不保证顺序                                       | 保证顺序                                   |
| 速度         | 通常比TCP快                                      | 通常比UDP慢                                |
| 头部大小     | 8字节                                            | 至少20字节                                 |
| 最大数据长度 | 65507字节 (实际应用中通常选用较小值，如1472字节) | 理论上65495字节，实际应用中通常1460字节    |
| 校验         | 可选                                             | 强制                                       |
| 用途         | 实时应用（如VoIP，视频流，实时游戏）             | 可靠传输（如文件传输，电子邮件，网页加载） |

[UDP传输的最大长度？](#UDP传输的最大长度？)

---

##### 什么是聚簇索引和非聚簇索引

在数据库中，聚簇索引和非聚簇索引是两种常见的索引策略，它们都可以使用B+树数据结构来实现。

1. **聚簇索引**：数据库表中的数据行在磁盘上的物理存储顺序和某一列（通常是主键列）或几列的逻辑（值）顺序相同，这种存储方式就是聚簇索引。也就是说，聚簇索引的叶节点就是实际的数据行。在一个表中，只能有一个聚簇索引，因为数据行只能按照一种顺序进行物理存储。当我们通过聚簇索引进行查找时，实际上是在B+树上进行查找，最终找到的叶节点就是我们需要的数据行。

2. **非聚簇索引**：非聚簇索引（也称为二级索引）是指索引的顺序和磁盘上行的物理存储顺序不同。非聚簇索引的叶节点存储的是对应数据行的主键值（或者是一个指向数据行的指针）。当我们通过非聚簇索引进行查找时，首先在B+树上找到对应的主键值，然后再通过这个主键值在聚簇索引上进行查找，找到实际的数据行。这就是为什么非聚簇索引的查找通常需要两次磁盘I/O，因此也被称为"覆盖索引"。

所以，一个表通常会有多棵B+树，一棵是聚簇索引的B+树，其他的是非聚簇索引的B+树。