

### bio

阻塞io



TCP服务端

```java
// 启动
ServerSocket serverSocket = new ServerSocket(8080);
while (!serverSocket.isClosed()) {
  Socket request = serverSocket.accept();
   
   //一次请求分配一个线程:
   threadPool.execute(() -> {
       // 处理接收到的内容inputStream
        InputStream inputStream = request.getInputStream();
    		inputStream.read(...)
        ...

        // 向客户端发送内容
        OutputStream outputStream = request.getOutputStream();
        outputStream.write(...)
         ...
   } 
                      
   ...
     
  // 关闭     
  serverSocket.close();
}

```

TCP客户端

```java
// 启动连接
Socket s = new Socket("localhost", 8080);
// 发送内容
OutputStream out = s.getOutputStream();
outputStream.write(...)

// 处理接收的内容inputStream
InputStream inputStream = s.getInputStream();
inputStream.read(...)
...

// 关闭客户端
s.close();


```

Udp服务端

```java
//服务端在3000端口监听接收到的数据
DatagramSocket ds = new DatagramSocket(3000);
接收数据
DatagramPacket dp_receive = new DatagramPacket(buf, 1024);
ds.receive(dp_receive);
发送数据
DatagramPacket dp_send= new DatagramPacket(str_send.getBytes(),str_send.length(),InetAddress dp_receive.getAddress() ,9000);
ds.send(dp_send);
```

Udp客户端 

和服务端等价，一样的写法

```java
DatagramSocket ds = new DatagramSocket(9000);
。。。
```





### Nio

非阻塞io

服务端

```java
 // 1. 创建服务端的channel对象
ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
serverSocketChannel.configureBlocking(false); // 设置为非阻塞模式
 // 2. 创建Selector
        Selector selector = Selector.open();

// 3. 把服务端的channel注册到selector，注册accept事件
        SelectionKey selectionKey = serverSocketChannel.register(selector, 0);
        selectionKey.interestOps(SelectionKey.OP_ACCEPT);

        // 4. 绑定端口，启动服务
        serverSocketChannel.socket().bind(new InetSocketAddress(8080)); // 绑定端口


while (true) {
            // 5. 启动selector（管家）
            selector.select();// 阻塞，直到事件通知才会返回

  					// 6，业务处理。 下面分配 subReactor线程处理
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                iterator.remove();

                if (key.isAcceptable()) {
                    SocketChannel socketChannel = ((ServerSocketChannel) key.channel()).accept();
                    socketChannel.configureBlocking(false);
                    socketChannel.register(selector, SelectionKey.OP_READ);
                    System.out.println("收到新连接：" + socketChannel);
                } else if (key.isReadable()) {// 客户端连接有数据可以读时触发
                    try {
                        SocketChannel socketChannel = (SocketChannel) key.channel();

                        ByteBuffer requestBuffer = ByteBuffer.allocate(1024);
                        while (socketChannel.isOpen() && socketChannel.read(requestBuffer) != -1) {
                            // 长连接情况下,需要手动判断数据有没有读取结束 (此处做一个简单的判断: 超过0字节就认为请求结束了)
                            if (requestBuffer.position() > 0) break;
                        }
                        if (requestBuffer.position() == 0) continue; // 如果没数据了, 则不继续后面的处理
                        requestBuffer.flip();
                        byte[] content = new byte[requestBuffer.remaining()];
                        requestBuffer.get(content);
                        System.out.println(new String(content));
                        System.out.println("收到数据,来自：" + socketChannel.getRemoteAddress());
                        // TODO 业务操作 数据库 接口调用等等

                        // 响应结果 200
                        String response = "HTTP/1.1 200 OK\r\n" +
                                "Content-Length: 11\r\n\r\n" +
                                "Hello World";
                        ByteBuffer buffer = ByteBuffer.wrap(response.getBytes());
                        while (buffer.hasRemaining()) {
                            socketChannel.write(buffer);
                        }
                    } catch (Exception e) {
                        e.printStackTrace();
                        key.cancel();
                    }
                }
            }
        }
```



客户端

```java
 // 1. 创建客户端channel对象
SocketChannel socketChannel = SocketChannel.open();
socketChannel.configureBlocking(false);
 // 2. 创建Selector
Selector selector = Selector.open();
socketChannel.register(selector, SelectionKey.OP_CONNECT);
//3 连接
socketChannel.connect(new InetSocketAddress("localhost", 8080));
 
while (true) {
  //4 selector接收事件，
  selector.select();
  Set<SelectionKey> selectionKeys = selector.selectedKeys();
  Iterator<SelectionKey> iterator = selectionKeys.iterator();
  
  // 5下面需要分配线程处理
  while (iterator.hasNext()) {
    SelectionKey selectionKey = iterator.next();
    iterator.remove();

    if (selectionKey.isConnectable()) { // 连接到远程服务器
      try {
        if (socketChannel.finishConnect()) { // 完成连接
          // 连接成功
          System.out.println("连接成功-" + socketChannel);

          ByteBuffer buffer = ByteBuffer.allocateDirect(20480);

          // 切换到感兴趣的事件
          selectionKey.attach(buffer);
          selectionKey.interestOps(SelectionKey.OP_WRITE);
        }
      } catch (IOException e) {
        // 连接失败
        e.printStackTrace();
        return;
      }
    } else if (selectionKey.isWritable()) {// 可以开始写数据
      ByteBuffer buf = (ByteBuffer) selectionKey.attachment();
      buf.clear();
      Scanner scanner = new Scanner(System.in);
      System.out.print("请输入：");
      // 发送内容
      String msg = scanner.nextLine();
      scanner.close();

      buf.put(msg.getBytes());
      buf.flip();

      while (buf.hasRemaining()) {
        socketChannel.write(buf);
      }

      // 切换到感兴趣的事件
      selectionKey.interestOps(SelectionKey.OP_READ);
    } else if (selectionKey.isReadable()) {// 可以开始读数据
      // 读取响应
      System.out.println("收到服务端响应:");
      ByteBuffer requestBuffer = ByteBuffer.allocate(1024);

      while (socketChannel.isOpen() && socketChannel.read(requestBuffer) != -1) {
        // 长连接情况下,需要手动判断数据有没有读取结束 (此处做一个简单的判断: 超过0字节就认为请求结束了)
        if (requestBuffer.position() > 0) break;
      }
      requestBuffer.flip();
      byte[] content = new byte[requestBuffer.remaining()];
      requestBuffer.get(content);
      System.out.println(new String(content));
      //                    selectionKey.interestOps(SelectionKey.OP_WRITE);

    }
  }
}
```

