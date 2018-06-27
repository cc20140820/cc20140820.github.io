---
layout: post
title:  Java socket(udp)
date: 2018-06-26 18:00:24.000000000 +09:00
---

>毕设花了2个月终于做完了，期间陆陆续续学了不少网络编程方面的知识，还是感谢自己独立完成了毕设，为了做个纪念，记录一下毕设过程中的所得。

1.**udp通信**

- 服务器广播并接收

```
int port=8888;    //目的端口
InetAddress address0 = InetAddress.getByName("255.255.255.255");    //广播地址
byte[] data0 = {(byte)0x55,(byte)0xBB,(byte)0xBC,(byte)0xCC};       //数据报头
DatagramPacket packet = new DatagramPacket(data0, data0.length, address0, port);
DatagramSocket socket = new DatagramSocket(8800);//服务器端口
socket.send(packet);

byte[] data = null;
while(true){
    try {
        data = new byte[9];   //创建字节数组，指定接收的数据包的大小
        packet = new DatagramPacket(data, data.length);
        socket.setSoTimeout(3000);    //设置阻塞超时
        socket.receive(packet);   //此方法在接收到数据报之前会一直阻塞
        Thread thread = new Thread(new UDPMacThread(socket, packet));
        thread.start();
        count++;
    }catch (SocketTimeoutException e){
        break;
    }
}
socket.close();
```

- 解析数据并入库(多线程)

```
public class UDPMacThread implements Runnable {

    IModuleService moduleService=(IModuleService) SpringContextUtil.getBean("moduleService");

    DatagramSocket socket = null;
    DatagramPacket packet = null;

    public UDPMacThread(DatagramSocket socket,DatagramPacket packet) {
        this.socket = socket;
        this.packet = packet;
    }

    public void run() {
        byte[]old=packet.getData();
        int length=packet.getLength();
        byte[]second=new byte[length-3];
        int[]fresh=new int[length];

        for(int i=0;i<3;i++){
            fresh[i]=old[i] & 0xff;
        }

        for(int i=0;i<length-3;i++){
            second[i]=old[i+3];
        }

        if(fresh[0]==85 && fresh[1]==170 && fresh[2]==172){
            String mac=HexUtil.bytesToHexFun1(second);
            String ip=packet.getAddress().getHostAddress();
            Info info=new Info();
            info.setMac(mac);
            info.setIp(ip);
            Result result=moduleService.insertMac(info);
        }else {
            return;
        }
    }
}
```
