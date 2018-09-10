<p align="center">
    <a href="https://www.lua.org/">
	    <img src="https://www.lua.org/images/lua25.gif" width="120" height="100">
	</a>
	<a href="https://unity3d.com/cn/">
	    <img src="https://huailiang.github.io/img/unity.jpeg" width="200" height="100">
	</a>
    	<a href="https://huailiang.github.io/">
    	<img src="https://huailiang.github.io/img/avatar-Alex.jpg" width="120" height="100">
   	</a>
</p>

旨在提供一套通用的方式生成lua的库


作者在windows上编译win x64 lua dll, 在mac上编译Android(.so)，osx(.bundle), ios(.a)平台的库

windows 上编译需要安装cmake, vs2013(当然也可以是其他版本，只不过作者在build/make_win64_luajit.bat make_win64_luajit.bat脚本使用的是vs2013, 读者可以根据自己本地环境修改里面定义的变量)

git工程一共有个分支：

1. master 使用的是lua5.3  lua53好处是最新的版本提供了对64int( long long)等新特性的支持，坏处是效率没有jit高
2. jit    使用的是lua5.1 对应的是luajit, 对应lua版本是lua5.1 好处是jit的运行效率高 坏处是jit作者早就不维护了


项目c#代码借鉴了很多ulua部分，在ulua升级lua库的时候，因为lua升级特性的原因，做了如下的修改：
1. LUA_REGISTRYINDEX  lua51对应的值是LUA_REGISTRYINDEX 在lua53的定义如下：
``` c++
#if LUAI_BITSINT >= 32
#define LUAI_MAXSTACK		1000000
#else
#define LUAI_MAXSTACK		15000
#endif
#define LUA_REGISTRYINDEX	(-LUAI_MAXSTACK - 1000)
```
所以lua53中对应的值是-1001000  这个值需要在LuaDLL.cs中定义修改过来

2. LUA_ENVIRONINDEX

从 Lua 5.2开始, LUA_GLOBALSINDEX 就不存在了. 代替的是, 使用lua_setglobal() and lua_getglobal(). 正确使用方式如下：


```
lua_pushstring(L, T::className);
lua_pushvalue(L, methods);
lua_settable(L, LUA_GLOBALSINDEX);
``

3. 根据我们项目的需要 移除了luasocket的库， 因为我们项目中所有的收发消息都是通过c#来，网络消息过来的时候，根据注册表分别向c++(战斗使用的库GameCore), lua(补丁使用的库)，c#(系统逻辑)转发， 不同平台使用对应的protobuf来反序列化出相应的对象。 读者可以根据自己项目的需要来定制自己的lua库。
