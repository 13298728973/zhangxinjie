### socket

```c++
#define WINSOCK_DEPRECATED_NO_WARNINGS 1
#include <iostream>
#include <winsock2.h>
#include <windows.h>
using namespace std;
#pragma comment(lib,"WS2_32.lib")
int main()
{
    cout << "TCP SERVER:" << endl;
    
    
    WSADATA wsaData;//存储请求的Socket的版本信息,存储被WSAStartup函数调用后返回的Windows Sockets数据
    
    
WORD wsaVersion=MAKEWORD(2, 0);//程序请求使用的Socket版本，其中高位字节指明副版本、低位字节指明主版本;
    
WSAStartup(wsaVersion,&wsaData);//完成对Winsock服务的初始化，因此需要调用WSAStartup函数。使用Socket的程序在使用Socket之前必须调用WSAStartup函数。
    
    
    
    
    

SOCKET serverSock=socket(AF_INET,SOCK_STREAM,0);//创建socket。第一个参数:一个地址描述，只能是AF_INE，代表TCP/IP协议族；第二个参数：新socket的类型描述,SOCK_STREAM是TCP类型，SOCK_DGRAM是UDP类型；第三个参数：socket所用的协议参数,如调用者不想指定，可用0指定，表示缺省，对于使用一给定地址族的某一特定套接口，只支持一种协议。但地址族可设为AF_UNSPEC(未指定)，这样的话协议参数就要指定了。协议号特定于进行通讯的"通讯域"。
    
    
    /*sockaddr的初始化*/
    sockaddr_in addr;//定义一个sockaddr对象
    
    addr.sin_family=AF_INET;//sin_family指代协议族，在socket编程中只能是AF_INET
    
    addr.sin_port=htons(9999);//设置端口号，并转换为网络字节顺序，htons是将整型变量从主机字节顺序转变成网络字节顺序， 就是整数在地址空间存储方式变为高位字节存放在内存的低地址处。网络字节顺序是TCP/IP中规定好的一种数据表示格式
    
    addr.sin_addr.S_un.S_addr=INADDR_ANY;//sin_addr存储IP地址，使用in_addr这个数据结构，s_addr按照网络字节顺序存储IP地址，INADDR_ANY设置了任意IP地址，也就是表示本机的所有IP
    
    bind(serverSock,(sockaddr*)&addr,sizeof(addr));//将socket名字、端口号和ip绑定到一起，绑定的一定是自己的 ip和和端口，第一个参数表示socket名字，第三个参数表示所指定的结构体变量的大小，第二个参数表示struct sockaddr的地址，用于设定要绑定的ip和端口，是一个指针，强制转换为struct sockaddr类型
    
    
    
    
listen(serverSock,100);//创建一个套接口并监听申请端口号的连接,第一个参数是socket名字，第二个参数是可以接收的最大连接数。
    
cout<<"tcp server is listen at port 9999:" <<endl; 
    
    
    

SOCKET clientSock;//创建了一个客户端socket
    
sockaddr_in clientaddr;//定义一个客户端sockaddr对象
    
int len=sizeof(sockaddr_in);//存有addr地址长度的整型数
char buf[512];
 char recvbuf[512]={0};    
while(true)//让服务器不停地监听客户端
    {

    //创建一个与s同类的新的套接口并返回句柄
  clientSock=accept(serverSock,(sockaddr*)&clientaddr,&len)//第一个参数是监听客户端的服务端的socket名字，第二个参数是指针，客户端地址，第三个参数是指针，配合addr一起使用，指向存有addr地址长度的整型数。

  if(INVALID_SOCKET != clientSock)//阻塞:阻塞调用是指调用结果返回之前，当前线程会被挂起。非阻塞和阻塞的概念相对应，指在不能立刻得到结果之前，该函数不会阻塞当前线程，而会立刻返回。如果没有错误产生，则accept()返回一个描述所接受包的SOCKET类型的值。否则的话，返回INVALID_SOCKET错误。该程序为非阻塞型。
      
  {
      cout <<"client: "<< inet_ntoa(clientaddr.sin_addr) <<" "<<clientaddr.sin_port << "is connected"<<endl//第一个输出客户端ip地址，调用函数是将网络ip地址转换成“.”点隔的字符串格式，第二个输出客户端端口号
          
  recv (clientSock,recvbuf,sizeof(recvbuf),0);//第一个参数指定接收端socket名字；第二个参数指明一个buf，该缓冲区用来存放recv函数接收到的数据；第三个参数指明buf的长度；第四个参数一般置0。
      
   cout <<recvbuf<<endl;
      
   cin>>buf;
      
   send(clientSock,buf,strlen(buf),0);//第一个参数是指定发送端socket描述符;第二个参数指明一个buf，该缓冲区用来存放输入的数据；第三个参数指明buf的长度；第四个参数一般置0。



      
      
      closesocket(clientSock);//关闭客户端
  }
}

  closesocket(serverSock);//关闭一个服务端
    
  WSACleanup();//终止Winsock 2 DLL (Ws2_32.dll) 的使用
return 0;
}//服务端
```

​	