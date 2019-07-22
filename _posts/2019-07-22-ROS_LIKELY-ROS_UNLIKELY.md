---
layout: post
title: "ROS_LIKELY 与 ROS_UNLIKELY宏"
tagline: "ros"
description: "None"
tags: ["ROS_LIKELY", "ROS_UNLIKELY", "likely", "unlikely"]
---

Linux内核中的linkely与unlikely:
--------------------------------
```
#define likely(x) __builtin_expect(!!(x), 1) //x很可能为真       
#define unlikely(x) __builtin_expect(!!(x), 0) //x很可能为假
```
```
if(likely(value))    //等价于 if(value) 但执行if分支可能性大(value为真可能性大)
if(unlikely(value))  //也等价于 if(value) 但执行else分支可能性大(value为假可能性大)
```

ROS里的宏:
```
#ifdef WIN32
#define ROS_LIKELY(x)       (x)
#define ROS_UNLIKELY(x)     (x)
#else
#define ROS_LIKELY(x)       __builtin_expect((x),1)
#define ROS_UNLIKELY(x)     __builtin_expect((x),0)
#endif
```
__builtin_expect
---------------------
是 GCC (version >= 2.96）提供给程序员使用的，目的是将“分支转移”的信息提供给编译器，这样编译器可以对代码进行优化，以减少指令跳转带来的性能下降。  
`__builtin_expect((x),1)`表示 x 的值为真的可能性更大；  
`__builtin_expect((x),0)`表示 x 的值为假的可能性更大。

ROS code sample:
---------------------
```
#define ROSCONSOLE_AUTOINIT \
   do \
   { \
     if (ROS_UNLIKELY(!::ros::console::g_initialized)) \
     { \
       ::ros::console::initialize(); \
     } \
   } while(0)
````
`!::ros::console::g_initialized`为真的可能性比较大,即`::ros::console::g_initialized`为假的可能性比较大,即`ROSCONSOLE_AUTOINIT`大概率不用执行初始化.
