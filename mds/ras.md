# Ras 插件  

实现remote access service的AppService模块，通过IAsynNetwork.CreateAppService加载插件  

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

```c++  
HRESULT __stdcall CreateTaskevents( /*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IUnknown** param1,  
      /*[in ]*/handle param2,  
      /*[in ]*/IAsynFrameThread* thread,  
      /*[out]*/IAsynMessageEvents** object )  
```  
## 参数
*[in ]param1*
*[in ]param2*
*[in ]thread*
*[out]object*

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。

## 备注

## 开发  
枚举网络连接名称  
```c++  
asynsdk::CStringSetter  ras(1, "com.svc.ras");
asynsdk::CStringVector  out(1);
spAsynFrameThread->Dispatch(0, &ras, AF_CTASK_NOTIFY, 0, 0, &out);
for(int i = 0; i < out.m_val.size(); ++ i)
{
    printf("%d: %s\n", out.m_val[i].c_str());
}
```  
创建Ras服务对象  
```c++  
CComPtr<IAppService> spAppService;

CKeyvalSetter params(1);
spAsynNetwork->CreateAppService(STRING_from_string("com.svc.dtp"), (IUnknown**)&params.p, STRING_from_string(ras), 0, (IAppService**)&spAppService);

spAppService->Control(1); //开始拨号
```  

## 例子  