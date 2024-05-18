# Proxy 插件  

提供端侧ftp/http/socks协议代理功能，支持INet/IAsynTcpSocket/IAsynRawSocket/IAsynProxySocket等接口

## 导出函数  
```c++  
HRESULT __stdcall CreateAsynPtlSocket(/*[in ]*/IAsynNetwork* lpAsynNetwork,  
      /*[in ]*/IAsynRawSocket* socket,  
      /*[in ]*/IUnknown**  param1,  
      /*[in ]*/const char* param2,  
      /*[in ]*/IAsynRawSocket** object )  
```  

## 参数
*[in]socket*  
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过IAsynNetwork.CreateAsynPtlSocket转调Proxy.CreateAsynPtlSocket接口  

## 开发
创建显式ftp over tls/普通ftp代理对象[client]：  
```c++  
CComPtr<IAsynTcpSocket> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocket(&spAsynInnSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("proxy"), spAsynInnSocket, 0, STRING_from_string(ssl_explicit? "ftps/1.0" : "ftp/1.0"), &spAsynPtlSocket);
```  

创建隐式ftp over tls代理对象[client]：  
```c++  
CComPtr<IAsynTcpSocket> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocket(&spAsynInnSocket);
CComPtr<IAsynRawSocket> spAsynSslSocket;
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("ssl"), spAsynInnSocket, 0, STRING_from_string("tls/1.0"), &spAsynSslSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("proxy"), spAsynSslSocket, 0, STRING_from_string("ftp/1.0"), &spAsynPtlSocket);
```  

创建http[s/t]代理对象[client]  
```c++  
CComPtr<IAsynTcpSocket> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocket(&spAsynInnSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("proxy"), spAsynInnSocket, 0, STRING_from_string(ssl? "https/1.0" : "http/1.0"), &spAsynPtlSocket);
```  

创建socks代理对象[client]  
```c++  
#define SOCKS_VERNO "5.0" //4.0 4.a 5.0
CComPtr<IAsynTcpSocket> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocket(&spAsynInnSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("proxy"), spAsynInnSocket, 0, STRING_from_string("socks/" SOCKS_VERNO), &spAsynPtlSocket);
```  

设置代理信息  
```c++  
CComPtr<IAsynProxySocket> spProxy; spAsynPtlSocket->QueryInterface(IID_IAsynProxySocket, (void **)&spProxy);
asynsdk::CKeyvalSetter params(1);
params.Set(STRING_from_string(";account"), 1, STRING_from_string(m_setsfile.get_string("proxy", "user") + ":" + m_setsfile.get_string("proxy", "password")));
spProxy->SetProxyContext(STRING_from_string(m_setsfile.get_string("proxy", "host", "127.0.0.1")), m_setsfile.get_long("proxy", "port", 8080), STRING_EX::null, &params);
```  

## 例子  
[ftpx client](https://github.com/netsecsp/aftpx)  
[http client](https://github.com/netsecsp/ahttp)  