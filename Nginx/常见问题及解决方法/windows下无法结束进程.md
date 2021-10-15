### 问题描述

打开任务管理器，找到nginx程序，无法结束进程

### 问题排查及解决

```shell
# 需要在 nginx.exe 所在的目录下
# 手动结束进程
nginx.exe -s stop
# 报错: nginx: [error] OpenEvent("Global\ngx_stop_12680") failed (2: The system cannot find the file specified)

# 查找 nginx 监听的 pid
netstat -ano

# 根据查询到 pid 核实是否是 nginx 服务, pid 必须用英文双引号
tasklist|findstr "PID"

# kill
taskkill /f /t /im nginx.exe
```
