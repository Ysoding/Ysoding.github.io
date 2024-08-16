
环境

1. 一种docker 拉一个 ubuntu:24.04的image创建容器, 在这个环境中按照 [https://docs.ros.org/en/rolling/Installation/Ubuntu-Install-Debians.html](https://docs.ros.org/en/rolling/Installation/Ubuntu-Install-Debians.html)， 安装 Debian的包
2. 直接拉取 ros:rolling 的 image 创建容器， 这里面环境都配好

基于ubuntu22.04 从源码编译Ros2 失败。

```sh
root@430981f8a34f:~/ros2_rolling# ros2
Traceback (most recent call last):
  File "/root/ros2_rolling/install/ros2cli/bin/ros2", line 33, in <module>
    sys.exit(load_entry_point('ros2cli', 'console_scripts', 'ros2')())
  File "/root/ros2_rolling/install/ros2cli/bin/ros2", line 25, in importlib_load_entry_point
    return next(matches).load()
  File "/usr/lib/python3.10/importlib/metadata/__init__.py", line 171, in load
    module = import_module(match.group('module'))
  File "/usr/lib/python3.10/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "<frozen importlib._bootstrap>", line 1050, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1027, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1006, in _find_and_load_unlocked
  File "<frozen importlib._bootstrap>", line 688, in _load_unlocked
  File "<frozen importlib._bootstrap_external>", line 883, in exec_module
  File "<frozen importlib._bootstrap>", line 241, in _call_with_frames_removed
  File "/root/ros2_rolling/build/ros2cli/ros2cli/cli.py", line 22, in <module>
    from rclpy.executors import ExternalShutdownException
  File "/root/ros2_rolling/install/rclpy/lib/python3.10/site-packages/rclpy/executors.py", line 50, in <module>
    from rclpy.subscription import Subscription
  File "/root/ros2_rolling/install/rclpy/lib/python3.10/site-packages/rclpy/subscription.py", line 23, in <module>
    from rclpy.event_handler import SubscriptionEventCallbacks
  File "/root/ros2_rolling/install/rclpy/lib/python3.10/site-packages/rclpy/event_handler.py", line 25, in <module>
    from rclpy.logging import get_logger
  File "/root/ros2_rolling/install/rclpy/lib/python3.10/site-packages/rclpy/logging.py", line 21, in <module>
    from rclpy.impl.rcutils_logger import RcutilsLogger
  File "/root/ros2_rolling/install/rclpy/lib/python3.10/site-packages/rclpy/impl/rcutils_logger.py", line 41, in <module>
    from typing_extensions import Unpack
ImportError: cannot import name 'Unpack' from 'typing_extensions' (/usr/lib/python3/dist-packages/typing_extensions.py)

```

ubuntu24.04的源，公司代理访问不了，换成ubuntu22.04的ok  

ros提供的rolling,jazzy image有问题都需要执行

First ensure that the [Ubuntu Universe repository](https://help.ubuntu.com/community/Repositories/Ubuntu) is enabled.

```sh
apt install software-properties-common
add-apt-repository universe
```

但是走代理有时候还是访问不了 ros的 repository(改错了，根本没走代理，加了sudo导致环境变量变了)

```sh
E: Failed to fetch http://packages.ros.org/ros2/ubuntu/pool/main/r/ros-rolling-turtlesim/ros-rolling-turtlesim_1.9.1-1noble.20240713.222752_amd64.deb  Could not connect to packages.ros.org:80 (64.50.233.100), connection timed out Could not connect to packages.ros.org:80 (64.50.236.52), connection timed out Could not connect to packages.ros.org:80 (140.211.166.134), connection timed out
E: Internal Error, ordering was unable to handle the media swap
```

给docker 容器设置图形化  
host

```sh
sudo apt-get install x11-xserver-utils
xhost + 
 access control disabled, clients can connect from any host
 
docker run -it -v /etc/localtime:/etc/localtime:ro -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY -e GDK_SCALE -e GDK_DPI_SCALE --name ros2 ros:rolling
```
