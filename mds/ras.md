# Ras 插件  

实现remote access service的AppService模块  

## 导出函数  
```c++  
HRESULT __stdcall CreateAppService(/*[in ]*/IAsynNetwork* lpAsynNetwork,  
         /*[in ]*/IUnknown* object,  
         /*[in ]*/IKeyvalSetter* param1,  
         /*[in ]*/const char*    param2,  
         /*[out]*/IAppService** object )  
```  
## 参数
*[in]object*  
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

```c++  
HRESULT __stdcall CreateObject( /*[in ]*/InstancesManager* lpInstancesManager,  
      /*[in ]*/IUnknown* param1,  
      /*[in ]*/uint64_t  param2,  
      /*[out]*/IUnknown** object)  
```  
## 参数
*[in, opt]param1*  
*[in, opt]param2*  
*[out]object*  

## 返回值
S_OK表示执行成功，其他值表示执行失败。  

## 备注
通过IAsynNetwork.CreateAppService转调Ras.CreateAppService接口  
通过InstancesManager.Execute转调Ras.Execute接口  

## 开发  
枚举网络连接名称  
```c++  
asynsdk::CStringSetter  ras(1, "com.svc.ras");
CComPtr<IStringVector>  out;
lpInstancesManager->NewInstance(&ras, 0, (IUnknown**)&out);
STRING v;
for(int i = 0; out->Get(i, &v); ++ i)
{
    printf("%d: %.*s\n", i, v.len, (const char*)v.ptr);
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