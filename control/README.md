# control
ros2

## 编译安装

mkdir build
cd build && cmake .. && make

## 打包节点stand逻辑说明

当前的节点自启动`stand`状态改为`保持当前位姿进入stand状态`，开control_node自动进入，也可以直接指令`stand`使用

若想使用为原本的站立位姿，指令为
```
ros2 service call /gait/gait_mode_srv kpl_msgs/srv/String "message: 'resume_stand'"
```
