# 树莓派MQTT客户端安装

## 1.直接安装

```
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade
sudo apt-get install mosquitto mosquitto-clients paho-mqtt
```

## 2. 或手段安装

### 2.1 下载

```
CD MQTT 
git init 
git pull https://github.com/eclipse/mosquitto.git
```

### 2.2 安装

编译前需安装必须的相关包

```
sudo apt-get update
sudo apt-get install libssl-dev uuid-dev xsltproc docbook-xsl libc-ares-dev libc-ares2
```

编译 安装

```
make
sudo make install
```

如有问题自行搜索解决。

### 测试运行

启动mosquitto服务。

`mosquitto -v`

得到如下结果

```
1504517896: mosquitto version 1.4.14 (build date 2017-09-04 16:57:39+0800) starting
1504517896: Using default config.
1504517896: Opening ipv4 listen socket on port 1883.
1504517896: Opening ipv6 listen socket on port 1883.
```

## 配置

配置文件在

_mosquitto/mosquitto.conf_

修改好后重启服务。

`sudo systemctl restart mosquitto`

# 客户端安装

有多种客户端，选择其中两个介绍。

## 1.mosquitto-clients客户端

```
sudo apt-get install mosquitto-clients
```

订阅端通过mosquitto\_sub订阅指定主题的消息.

```
mosquitto_sub -h hostip -p 1883 -t test -i respi
```

* -t:订阅的主题（title）

发布者通过mosquitto\_pub发布指定主题的消息。

`mosquitto_pub -h hostip -t test -m "This is a test message from Pi"`

转发服务器把该主题的消息推送到订阅端。

## 2. Python客户端

```
sudo pip install paho-mqtt
```



