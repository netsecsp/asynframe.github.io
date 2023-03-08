# Console 插件  

提供命令控制台，支持键盘/鼠标输入，实现IOsCommand接口，便于产品在线调试  

## 导出函数  
```c++  
HRESULT __stdcall CreateAsynFramePlugin( /*[out]*/IAsynFramePlugin** object )    
```  

## 参数
*[out]object*  

## 返回值
S_OK表创建对象成功，其他值表示创建对象失败。  

## 备注
通过InstancesManager.Require转调Console.CreateAsynFramePlugin接口  

## 开发  
加载插件
```c++  
if( lpInstancesManager->Require(STRING_from_string(IN_Console)) == S_OK )
{// 加载成功
    CComPtr<IConsole> console; lpInstancesManager->GetInstance(STRING_from_string(IN_Console), IID_IConsole, (void**)&console);

    console->AllocWindow(spAsynFrameThread, asynsdk::STRING_EX::null, ENABLE_INPUT_KEYBOARD|ENABLE_INPUT_MOUSE, 0); //创建控制台, 支持键盘/鼠标输入
}
```  

## 例子  
\support\testconsole  
