# navigation_frame 导航框架

```
开发人员：杨工

开发记录：
————2023年4月19日
1、导航框架第一个版本，ROS封装，添加日志系统
2、添加任务模块

————2023年4月20日
1、添加机器人模型，四轮底盘
2、添加遥控节点
3、添加arbotix仿真插件

————2023年4月22日
1、添加当前位置获取
2、添加串口通信代码

————2023年5月1日
1、完善录制线路，发布线路的代码，仿真验证通过

————2023年5月2日
1、完善跟线行使，仿真验证通过，状态机还有问题
2、无法获取launch的参数，未解
3、小车的状态信息还没通过topic发送出去

————2023年5月5日
1、增加串口通信，测试通过，可以收发数据
2、状态机和界面切换任务未完善
```

**整体思路**

打算写一个新的导航通用框架，将各个功能模块连接起来

**目录结构**

```
.
├── arbotix_ros 仿真插件
├── atom 小车模型
├── navigation 导航系统
│   ├── include
│   │   ├── context 连接上下文
│   │   │   └── context.h
│   │   ├── logger 日志系统
│   │   │   └── logger.h
│   │   ├── mission 任务收发
│   │   │   └── mission.h
│   │   ├── task 任务
│   │   │   ├── path_task.h
│   │   │   └── task.h
│   │   └── utility_tool 通用工具
│   │       └── utility_tool.h
│   ├── package.xml
│   ├── src
│   │   ├── context
│   │   │   └── context.cpp
│   │   ├── logger
│   │   │   └── logger.cpp
│   │   ├── main.cpp
│   │   ├── mission
│   │   │   └── mission.cpp
│   │   └── task
│   │       ├── path_task.cpp
│   │       └── task.cpp
│   └── srv
│       └── path.srv
└── teleop_twist_keyboard_cpp 键盘控制程序
```

**启动步骤**

1、启动仿真：

```
$ roslaunch atom sim_navigation.launch
```

2、启动导航程序：

```
$ roslaunch navigation navigation.launch
```

3、启动录制线路程序（1-1000 代码录制第几条线路，0 代表暂停录制）：

```
$ rosrun navigation record_path_client 1
```

4、启动键盘控制：

```
$ rosrun teleop_twist_keyboard_cpp teleop_twist_keyboard
```

5、发布录制线路程序（1-1000 代码录制第几条线路，0 代表暂停发布）：

```
$ rosrun navigation pub_path_client 1
```

6、暂停小车，发送 true 暂停，发送 false 恢复行使

```
$ rostopic pub -1 /car_stop  std_msgs/Bool "data: true"
```

7、设置小车速度，单位是 m/s

```
$ rostopic pub -1 /set_speed std_msgs/Float64 "data: 0.3"
```

**实车图片**

![Image text](https://github.com/haicheng12/navigation_frame/blob/main/img/car_img.jpg)
![Image text](https://github.com/haicheng12/navigation_frame/blob/main/img/car_sim.png)

**完善思路**

```
1、任务收发使用ROS服务的形式
2、定位信息和传感器信息接收
3、zmq的形式修改通信架构，例如使用DDS的方式
4、交叉编译代码到linux板子
5、QT界面切换状态机的导航任务选择
```
