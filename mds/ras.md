# Ras 插件  

实现remote access service的AppService模块  

## 导出函数  
```c++  
HRESULT __stdcall CreateAppService(/*[in ]*/InstancesManager* lpIInstancesManager,  
         /*[in ]*/IUnknown* object,  
         /*[in ]*/IKeyvalSetter* param1,  
         /*[in ]*/const char* param2,  
         /*[out]*/IAppService** object )  
```  
## 参数
*[in]object*  
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

```c++  
HRESULT __stdcall CreateTaskEvents( /*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IAsynFrameThread* thread,  
      /*[in ]*/IUnknown* param1,  
      /*[in ]*/handle param2 )  
```  
## 参数
*[in]thread*  
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过IAsynNetwork.CreateAppService转调Ras.CreateAppService接口  
通过IAsynFrameThread.PostTask/Dispath转调Ras.CreateTaskEvents接口  

## 开发  
枚举网络连接名称  
```c++  
asynsdk::CStringSetter  ras(1, "com.svc.ras");
asynsdk::CStringVector  out(1);
spAsynFrameThread->Dispatch(0, &ras, AF_QUERY_RESULT, 0, 0, &out);
for(int i = 0; i < out.m_val.size(); ++ i)
{
    printf("%d: %s\n", out.m_val[i].c_str());
}
```  
创建Ras服务对象  
```c++  
CComPtr<IAppService> spAppService;

const char *ras = "拨号连接";
CKeyvalSetter params(1);
spAsynNetwork->CreateAppService(STRING_from_string("com.svc.dtp"), 0, &params, STRING_from_string(ras), (IAppService**)&spAppService);

spAppService->Control(ST_ActStart); //开始拨号
```  

## 例子  