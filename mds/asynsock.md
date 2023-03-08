# Asynsock 插件  

提供dns/tcp/udp等网络功能  

## 导出函数  
```c++  
HRESULT __stdcall CreateAsynFramePlugin( /*[out]*/IAsynFramePlugin** object )    
```  

## 参数
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过InstancesManager.Require转调Asynsock.CreateAsynFramePlugin接口  

## 开发  
加载插件
```c++  
if( lpInstancesManager->Require(STRING_from_string(IN_AsynNetwork)) == S_OK )
{// 加载成功
}
```  

## 例子  
\support\testnetclient  
\support\testnetserver  
