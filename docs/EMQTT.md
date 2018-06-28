## Centos 安装EMQ

RPM安装

`rpm -ivh --force`[`http://emqtt.com/downloads/latest/centos6-rpm`](http://emqtt.com/downloads/latest/centos6-rpm)

Erlang/OTP R19依赖lksctp-tools库

`yum install lksctp-tools`

启动停止  
`service emqttd start|stop|restart`

查看状态  
`emqttd_ctl status`

控制台调试模式启动，检查 EMQ 是否可正常启动:  
`emqttd console`

系统重启时自动启动  
`sudo systemctl enable mosquitto`

配置文件  
EMQ配置文件: /etc/emqttd/emq.conf，插件配置文件: /etc/emqttd/plugins/\*.conf。

## 使用

`service emqttd start` 启动后，通过[http://IP:18083/即可访问后台，默认用户名admi，密码public。](http://IP:18083/即可访问后台，默认用户名admi，密码public。)

Windows下使用mqtt-spy作为客户端测试连接。只需设置IP，端口，WebSockets两项：![](/assets/mqtt-spysetting.png)

[http://45.32.32.55:18083/](http://45.32.32.55:18083/)  
不使用代理无法访问此链接，使用代理可以

怀疑端口设置问题，关闭防火墙可以了。确认与shadowsocks是否运行无关。

