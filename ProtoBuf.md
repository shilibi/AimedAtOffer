## ProtoBuf（使用vcpkg进行安装）

## 一、安装protobuf

## 1. 安装vcpkg

1. 创建文件夹 C:\src\vcpkg，进入C:\src\vcpkg
2. git clone https://github.com/microsoft/vcpkg
3. 运行bootstrap-vcpkg.bat 等待生成 vcpkg.exe
4. 在环境变量Path中添加C:\src\vcpkg
5. 在cmd中输`vcpkg`验证是否成功
6. 于vs2017集成vcpkg ，在CMD输入：`vcpkg integrate install`

## 2. 给vs 2017 添加英文语言包

运行 Microsoft Visual Studio Install ,选择修改->语言包，勾选英文，点击修改，等待修改完成。

## 3. 安装protobuf

命令行运行： 

vcpkg install protobuf protobuf:x64-windows

## 4. 编译MyMessage

protoc MyMessage.proto  --cpp_out=.\   --experimental_allow_proto3_optional

## 二、protobuf编写

1、使用







写线程 心跳检测加入  定时器 or  sleep







