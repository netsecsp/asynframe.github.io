# Jvm 插件  

提供IOsCommand接口，支持IOsCommand/IScriptHost接口  

## 导出函数  
```c++  
HRESULT __stdcall CreateCommand( /*[in ]*/InstancesManager *lpInstancesManager, /*[in ]*/IUnknown *param1, /*[in ]*/uint64_t param2, /*[out]*/IOsCommand **ppObject)  
```  
## 参数
*[in ]param1*  
*[in, opt]param2*  
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过InstancesManager.NewInstance转调Jvm.CreateCommand接口  

## 开发  
创建Jvm对象
```c++  
asynsdk::CStringSetter jre("D:\\workplat\\gnu\\JDKv17\\x86");
asynsdk::CreateObject(lpInstancesManager, "com.command.jvm/jvmproxy", &jre, 0, IID_IOsCommand, (IUnknown**)&spCommand)
spCommand->Execute(0, STRING_from_string("com.demo.Testgui"), 0, 0, 0, 0); //执行java
```  

## 例子  
\support\testjava\testapi  
\support\testjava\testjvm  