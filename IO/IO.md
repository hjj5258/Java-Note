### IO

 分类：

- BIO

  - 输入流
    - Input（字节流）
    - Reader（字符流）
  - 输出流（可刷新，flush方法可刷新管道中的数据强行输出：清空管道）
    - Output（字节流）
    - Writer（字符流）
  - 字节流
    - 可读取任何文件，内部数据是二进制
  - 字符流
    - 只能读取纯文本文件

  

  具体包类：

  - 转换流（讲字节流转为字符流）

    - InputStreamReader
    - OutputStreamWriter

  - 包装流（自带缓冲，不需要byte[]、char[]）,只需close包装流实际流会自动关闭

    - BufferedReader
    - BufferedWriter
    - BufferedInputStream
    - BufferedOutputStream

  - 数据流（数据可以写入任何类型，）

    ```java
    dos.writeInt(i);
    dos.writeFloat(f);
    ```

    - DataInputStream
    - DataOutputStream

  - 标准输出流

    - PrintStream
    - PrintWrite

  - 对象流（序列化）

    - 对象实现 **Serializable**接口，对象中所有属性也必须是可序列化的，添加 **serialVersionUID**，对象修改时候版本

  - 对于不序列化属性可添加，**transient**关键字

    - ObjectInputStream  writeObject()

  - ObjectOutputStream  readObject()

  - 随机存储文件流（读/写），可跳跃指定位置开始，断点下载

    - RandomAccessFile  seek() 方法调整指针到指定位置开始

  - IO工具库，**Apache Commons IO**

- NIO

  - ![IO操作流程](C:\Users\yz\AppData\Roaming\Typora\typora-user-images\image-20210407112436644.png)
  
  - ![image-20210407115013167](C:\Users\yz\AppData\Roaming\Typora\typora-user-images\image-20210407115013167.png)
  
  - ![image-20210407114956597](C:\Users\yz\AppData\Roaming\Typora\typora-user-images\image-20210407114956597.png)
  
  - Buffer（缓冲区），对于原有的IO直接对数组操作上进行封装，更易于操作与管理，分为堆外内存（操作系统内存），堆内内存（JVM）
    - 容量（Capacity）  缓冲区能够容纳的数据元素的最大数量  
    - 上界（Limit）  缓冲区的第一个不能被读或写的元素。或者说，缓冲区中现存元素的计数  
    - 位置（Position）  下一个要被读或写的元素的索引。位置会自动由相应的 get( )和 put( )函数更新。 
    - 标记（Mark）  一个备忘位置。调用 mark( )来设定 mark = postion。调用 reset( )设定 position =
      mark。标记在设定前是未定义的(undefined)。  
    - 0 <= mark <= position <= limit <= capacity   
    - buffer.flip() 读取模式
    - MappedByteBuffer
    - 对于堆外内存（图1-6），会比堆内少一次拷贝，需要手动回收
    - 会自动扩容，类是ArrayList
    - read/write
  
- Channel（通道）
    - Channel to Channel，管道之间可以直接传输数据，transferTo()  transferFrom()
  - FIle类的包括{FileInputStream、FileOutputStream、RandomAccessFile}，获取管道
    - Socket（可设置非阻塞）{ServerSocketChannel、SocketChannel}

  - Selector（选择器），基于事件监听，需要手动的注册，通过register()
  
    - 多路复用，sselect 阶段是阻塞的，避免大量客户端连接时频繁的上下文切换
  
- SelectionKey
  
  - OP_READ
    - OP_WRITE
    - OP_CONNECT（连接）
    - OP_ACCEPT（接收）
  
  - 代码实现
  
    ````java
    Iterator iterator = selector.selectedKeys().iterator();
    while (iterator.hasNext()) {
                        SelectionKey key = (SelectionKey) iterator.next();
                        if (key.isAcceptable()) {
                            ServerSocketChannel server = (ServerSocketChannel) key.channel();
                            SocketChannel accept = server.accept();
                          // do somethink
                        }
                        if (key.isReadable()) {
                          SocketChannel socketChannel = (SocketChannel) key.channel();
                           // do somethink
                        }
                        iterator.remove();
                    }
    ````
  
    

- AIO（异步非阻塞）
  - 异步方式，应用操作直接返回，等处理完成后，操作系统通知相应的线程，回调的方式进行后续工作
  - AsynchronousServerSocketChannel、AsynchronousSocketChannel
  - CompletionHandler 回调函数