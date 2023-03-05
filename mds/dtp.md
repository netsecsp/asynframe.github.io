# Dtp 插件  

实现Tcp/Udp端口复用的网络框架的AppService模块，通过IAsynNetwork.CreateAppService加载插件  

## 导出函数  
```c++  
HRESULT __stdcall CreateAppService(/*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IUnknown** param1,  
      /*[in ]*/const char* param2,  
      /*[in ]*/IAsynMessageEvents* events,  
      /*[out]*/IAppService** object )  
```  
## 参数
*[in ]param1*
*[in ]param2*
*[in, opt]events*
*[out]object*

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。

## 备注

## 开发  

创建实现Tcp端口复用的Dtp服务对象：  
```c++  
CComPtr<IAsynTcpSocketListener> spAsynTcpSocketListener;
spAsynNetwork->CreateAsynTcpSocketListener(0, &spAsynTcpSocketListener);
spAsynTcpSocketListener->Open(m_spAsynFrameThread, AF_INET, SOCK_STREAM, IPPROTO_TCP);
spAsynTcpSocketListener->Bind(asynsdk::STRING_EX::null, port, TRUE, NULL);
if( port == 0 ) 
{
    spAsynTcpSocketListener->GetSockAddress(0, 0, &port, 0);
}
CComPtr<IDtpService> spDtpService;
spAsynNetwork->CreateAppService(STRING_from_string("com.svc.dtp"), (IUnknown**)&spAsynTcpSocketListener.p, STRING_from_string("rcvsize=64&minsize=16&timeout=5000"), 0, (IAppService**)&spDtpService);

//创建报文识别器

spDtpService->Control(ST_ActStart); //启动服务
```  

创建实现Udp端口复用的Dtp服务对象：  
```c++  
CComPtr<IAsynUdpSocket> spAsynUdpSocket;
spAsynNetwork->CreateAsynUdpSocket(&spAsynUdpSocket );
spAsynUdpSocket->Open(m_spAsynFrameThread, AF_INET, SOCK_DGRAM, IPPROTO_UDP);
spAsynUdpSocket->Bind(asynsdk::STRING_EX::null, port, TRUE, NULL);
if( port == 0 )
{
    spAsynUdpSocket->GetSockAddress(0, 0, &port, 0);
}

CComPtr<IDtpService> spDtpService;
spAsynNetwork->CreateAppService(STRING_from_string("com.svc.dtp"), (IUnknown**)&spAsynUdpSocket.p, STRING_from_string("mss=1500&obw=1000"), 0, (IAppService**)&spDtpService);

//创建报文识别器

spDtpService->Control(ST_ActStart); //启动服务
```  

创建报文识别器  
```c++  
class CTcpEvent_http : public asynsdk::asyn_message_events_impl
{
public:
    CTcpEvent_http(IAsynNetwork *lpAsynNetwork, IDtpService *lpService, IAsynFrameThread *lpAsynFrameThread)
    {
        CreateAsynFrame(lpAsynFrameThread, 0, &m_spAsynFrame);
        m_spDtpService = lpService;

        CComPtr<IAsynDtpSocketListener> spAsynDtpSocketListener;
        m_spDtpService->CreateAsynDtpSocketLayer(GetAsynMessageEvents(), 0, asynsdk::STRING_EX::null, 0, (IUnknown**)&spAsynDtpSocketListener);

        CComPtr<IAsynTcpSocketListener> spAsynTcpSocketListener;
        lpAsynNetwork->CreateAsynTcpSocketListener(spAsynDtpSocketListener, &spAsynTcpSocketListener);

        CComPtr<IAsynRawSocket> spAsynPtlSocket;
        lpAsynNetwork->CreateAsynPtlSocket(STRING_from_string("http"), (IUnknown**)&spAsynTcpSocketListener.p, STRING_from_string("tcp/1.1"), &spAsynPtlSocket)
        spAsynPtlSocket->QueryInterface(IID_IAsynTcpSocketListener, (void**)&m_spAsynTcpSocketListener);

        m_spAsynTcpSocketListener->Open(lpAsynFrameThread, AF_INET, SOCK_STREAM, IPPROTO_TCP);

        CComPtr<IAsynNetIoOperation> spAsynIoOperation; lpAsynNetwork->CreateAsynIoOperation(m_spAsynFrame, 0, 0, IID_IAsynNetIoOperation, (void**)&spAsynIoOperation);
        m_spAsynTcpSocketListener->Accept(spAsynIoOperation);
    }

    STDMETHOD(OnMessage)( /*[in]*/uint32_t lMessageId, /*[in]*/uint64_t lParam1, /*[in]*/uint64_t lParam2, /*[in]*/IUnknown** objects)
    {
        if( lMessageId == AF_QUERY_RESULT )
        {
            if( m_spDtpService == (IDtpService*)lParam1 )
            {// 识别http报文成功则返回S_OK
                std::string packet((char*)((STRING*)lParam2)->ptr, ((STRING*)lParam2)->len);
                return packet.find("HTTP/1.1") != std::string::npos? S_OK : E_NOTIMPL;
            }
            return E_NOTIMPL;
        }
        if( lMessageId == AF_IOMSG_NOTIFY )
        {
            if( lParam2 == Io_acceptd )
            {// http连接接入
                IAsynIoOperation* lpAsynIoOperation = (IAsynIoOperation*)objects[0];
                uint32_t lErrorCode, lTransferedBytes;
                lpAsynIoOperation->GetCompletedResult(&lErrorCode, &lTransferedBytes, 0);
                if( lErrorCode == NO_ERROR )
                {
                    ....
                }
            }
            return E_NOTIMPL;
        }
        return E_NOTIMPL;
    }

    CComPtr<IDtpService> m_spDtpService;
    CComPtr<IAsynFrame > m_spAsynFrame;
}
``` 

## 例子  
\support\testnetserver_dtp-tcp.port  
\support\testnetserver_dtp-udp.port  