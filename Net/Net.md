### 网络

 - InetAddress 网络地址：IP
   	- InetAddress.getByName("www.baidu.com")
 - 端口号（Http：80，MySQL：3306）
 - TCP网络编程
    - Socket，通过IP与端口绑定客户端，Output流
       - 也可以接收服务端的数据，如果使用的阻塞的IO，则需要shutdownOutput()
    - ServerSocket，通过端口、或者IP + 端口绑定，Input流读取
       - 也可以向客户端发送数据
 - URL，根据网络资源访问
    - 例子，下载网络图片
       - new URL();
       - openConnection()
       - urlConnection.connect()
       - urlConnection.getInputStream()
       - save file

