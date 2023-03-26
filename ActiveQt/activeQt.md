# Active Qt

qt提供了`QtActiveX`模块来支持微软`ActiveX`的开发。
具体来说，qt提供了两个模块来支持`ActiveX`的开发:
1. QAxContainer:QAxContainer是一个静态库，过QAxObject和QAxWidget分别支持COM对象和ActiveX控件的开发，可以通过这两个对象将外部的COM或者ActiveX组件接入到Qt应用程序
2. QAxServer:使用QAxServer模块，通过QAxAggregated、QAxBindable和QAxFactory类，通过了进程内和可执行程序exe两种方式的COM Server模式，用来将Qt写的内容导出为COM或者ActiveX供他人使用。


使用`QAxContainer`需要在pro文件中声明:

```cpp
QT += axcontainer
```

使用`QAxServer`需要在pro文件中声明:

```cpp
QT  += axserver
```

qt提供了以下官方示例:

* [Multiple Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-multiple-example.html)
* [Qutlook Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-qutlook-example.html)
* [COM App Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-comapp-example.html)
* [Dot Net Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-dotnet.html)
* [OpenGL Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-opengl-example.html)
* [Hierarchy Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-hierarchy-example.html)
* [Media Player Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-mediaplayer-example.html)
* [Menus Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-menus-example.html)
* [Wrapper Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-wrapper-example.html)
* [Simple Example (ActiveQt)](https://doc.qt.io/qt-5/activeqt-activeqt-simple-example.html)

# 使用QAxObject操作excel

