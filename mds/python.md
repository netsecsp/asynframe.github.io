# Python 插件  

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
通过InstancesManager.NewInstance转调Python.CreateCommand接口  

## 开发  
创建python对象
```c++  
asynsdk::CStringSetter home("C:\\Program Files (x86)\\python");
asynsdk::CreateObject(lpInstancesManager, "com.command.python/python312", &home, 0, IID_IOsCommand, (IUnknown**)&spCommand);
spCommand->Execute(0, STRING_from_string("open"), &STRING_from_string("test.py"), 1, 0); //执行test.py脚本
spCommand->Execute(0, STRING_from_string("print("This is my world!")"), 0, 0, 0); //执行python脚本块
```  

## 例子  
\support\testpython\testapi  
\support\testpython\testpython  