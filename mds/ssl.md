# Ssl 插件  

提供ssl握手/加密/解密处理功能，支持ISsl/IAsynTcpSocket/IAsynRawSocket/IAsynTcpSocketListener等接口

## 导出函数  
```c++  
HRESULT __stdcall CreateAsynPtlSocket(/*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IAsynRawSocket* socket,  
      /*[in ]*/IUnknown** param1,  
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
通过IAsynNetwork.CreateAsynPtlSocket转调Ssl.CreateAsynPtlSocket接口  

## 开发  
创建ssl对象[client]：  
```c++  
CComPtr<IAsynTcpSocket> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocket(&spAsynInnSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("ssl"), spAsynInnSocket, 0, STRING_from_string(tls? "tls/1.0" : "ssl/3.0"), &spAsynPtlSocket);
```  

创建ssl对象[server]：  
```c++  
CComPtr<IAsynTcpSocketListener> spAsynInnSocket;
m_spAsynNetwork->CreateAsynTcpSocketListener(0, &spAsynInnSocket);
m_spAsynNetwork->CreateAsynPtlSocket(STRING_from_string("ssl"), spAsynInnSocket, 0, STRING_from_string(tls? "tls/1.0" : "ssl/3.0"), &spAsynPtlSocket);
```  

设置p12证书  
```c++  
CComPtr< ISsl> spSsl;
spAsynPtlSocket->QueryInterface(IID_ISsl, (void**)&spSsl);

STRING certpasswd[2];
certpasswd[0] = STRING_from_string(p12_cert);
certpasswd[1] = STRING_from_string(password);
spSsl->SetCryptContext(0, 0, certpasswd);
```  

## 例子  
\support\testnetclient_ssl  
\support\testnetserver_ssl  