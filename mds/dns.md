# dns 插件  

提供基于udp/tcp/http的域名解析  

## 导出函数  
```c++  
HRESULT __stdcall CreateAsynDnsResolver(/*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IAsynMessageEvents* events,  
      /*[in ]*/IKeyvalSetter* param1,  
      /*[in ]*/const char*    param2,  
      /*[out]*/IAsynDnsResolver** object )  
```  

## 参数
*[in ]events*  
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过IAsynNetwork.CreateAsynPtlSocket转调Dns.CreateAsynPtlSocket接口  

## 开发  
uri格式：schema://[host1|..|hostn]:port/...  
1. 配置udp解析域名，udp://\*:53/  
2. 配置tcp解析域名，tcp://\*:53/  
3. 配置腾讯云解析域名，http://119.29.29.29/d?dn=[host].&ip=[ip]&ttl=1  
4. 配置阿里云解析域名，http://203.107.1.33/{account_id}/d?host=[host]&ip=[ip]&query=[af]  

创建dns 对象：  
```c++  
CComPtr<IAsynDnsResolver> spAsynDnsResolver;
const char *uri = "udp://8.8.8.8|8.8.4.4:53";
spAsynNetwork->CreateAsynDnsResolver(STRING_from_string("dns"), 0, 0, STRING_from_string(uri), &spAsynDnsResolver);
```  

提交解析请求：  
```c++  
CComPtr<IAsynNetIoOperation> spAsynIoOperation;
m_spAsynNetwork->CreateAsynIoOperation(m_spAsynFrame, af, 0, IID_IAsynNetIoOperation, (void **)&spAsynIoOperation);
if( spAsynIoOperation->SetHost(STRING_from_string(host), 1) != S_OK )
{// 需要解析域名
    return lpAsynDnsResolver->Commit(spAsynIoOperation, af) == S_OK;
}
else
{// 已是ipvx
    printf("ip: %s\n", host.c_str());
    return true;
}
```  

异步解析结果：  
```c++  
HRESULT CAsynDnsHandler::OnIomsgNotify( uint64_t lParam1, uint64_t lAction, IAsynIoOperation *lpAsynIoOperation )
{
    uint32_t lErrorCode = NO_ERROR;
    lpAsynIoOperation->GetCompletedResult(&lErrorCode, 0, 0 );

    switch(lAction)
    {
        case Io_hostdns:
        {
             if( lErrorCode != NO_ERROR )
             {// 解析失败
                 printf("dns, error: %d\n", lErrorCode);
             }
             else
             {// 解析成功
                 CComPtr<IStringsStack> spDnsResult;
                 HRESULT r = lpAsynIoOperation->GetCompletedObject(1, IID_IStringsStack, (void **)&spDnsResult);
                 asynsdk::CStringSetter addr(1);
                 for(int i = 0; spDnsResult->Pop(&addr) == S_OK ; ++ i)
                 {
                     printf("ip: %s\n", addr.m_val.c_str());
                }
                ....
             }
             break;
        }
    }
    return E_NOTIMPL;
}
```  

## 例子  
\support\testdns  