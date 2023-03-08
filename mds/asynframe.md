# asynframe  

基于异步消息驱动的windows[x86/x64]平台的SDK开发框架，提供线程、管道、文件和网络等基础接口。  
![image](https://netsecsp.github.io/img/01_asynframe.jpg)  

# 1. DoInit asynframe接口  
```c++  
HRESULT __stdcall  Initialize( /*[in ]*/IAsynMessageEvents *events, /*[in ]*/IKeyvalSetter *param2 ) 
```  
## 参数  
*[in, opt]events*  
用于插件模块通知App事件  
*[in, opt]param2*  
配置参数信息，默认自动加载应用数据目录下的config.ini文件  

## 返回值  
S_OK表示成功，其他值表示失败。  

## 备注  
要求param2的配置信息的键格式：module_name, 值的编码格式：utf8  

若param2配置以下键值时： 
1. key=";sysroot" 表示配置系统工作目录，默认asyncore.dll所在目录  
2. key=";appdata" 表示配置应用数据目录，默认c:\Users\\\<account>\AppData\Roaming\netsecsp\\\<AppName>  
3. key=";logprop" 表示配置Log4cplus配置文件，默认app所在目录的prop.txt
3. key="asyncore_uniqueopspool" 表示是否创建有名工作线程池  
4. key="asyncore_uniqueevtpool" 表示是否创建有名事件线程池  
5. key="asyncore_uniquenetpool" 表示是否创建有名网络线程池  
6. key="asynsock_net_iaf" 配置值2=ipv4/23=ipv6  
7. key="asynsock_dns_url" 表示配置默认dnsurl  
udp://\*:53 表示udp  
tcp://\*:53 表示tcp  
http://119.29.29.29/d?dn=[host].&ip=[ip]&ttl=1 表示使用腾讯云  

```c++   
CComPtr<IKeyvalSetter> configure;
InstancesManager::GetInstance(STRING_from_string(IN_SysArgv), IID_IKeyvalSetter, &configure);
configure->Travel(0); //表示把配置信息刷新到配置文件里  
```  

# 2. 获取实例管理器接口 
```c++ 
InstancesManager* __stdcall GetInstancesManager()  
```  
## 返回值  
返回实例管理器的<font color=red>引用指针</font>，App不能再调用Release释放  

# 3. UnInit asynframe接口
```c++ 
HRESULT __stdcall Destory()  
```  
## 返回值  
S_OK表示成功，其他值表示失败  
