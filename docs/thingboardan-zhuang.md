# ThingsBoard安装

分别介绍Windows和Centos下安装ThingsBoard



## Windows Docker 方式安装

### Installation steps

- [安装Docker Toolbox for Windows](https://docs.docker.com/toolbox/toolbox_install_windows/)

- 打开 "Docker Quickstart Terminal"

- 创建一个文件夹保存docker 文件:

- 从thingsboard repo下载下面三个文件:

  - **docker-compose.yml** - main docker-compose file.
  - **.env** - main env file that contains default location of cassandra data folder and cassandra schema.
  - **tb.env** - default thingsboard environment variables.

  ```
  curl -L https://raw.githubusercontent.com/thingsboard/thingsboard/release-2.0/docker/docker-compose.yml > docker-compose.yml
  curl -L https://raw.githubusercontent.com/thingsboard/thingsboard/release-2.0/docker/.env > .env
  curl -L https://raw.githubusercontent.com/thingsboard/thingsboard/release-2.0/docker/tb.env > tb.env
  ```

- If you have already installed ThingsBoard using docker and want to upgrade or cleanup your installation, please cleanup HSQLDB data directory

```
docker-machine ssh default 'rm -rf /home/docker/hsqldb_volume'
```

- If you would like to create system and demo data and to start ThingsBoard node execute next command

```
ADD_SCHEMA_AND_SYSTEM_DATA=true ADD_DEMO_DATA=true bash -c 'docker-compose up -d tb'
```

- In case you would like to skip creation of system and demo data or you have already added and you only need to start ThingsBoard node then execute *docker-compose* command

```
docker-compose up -d tb
```

- In order to get access to necessary resources from external IP/Host after ThingsBoard docker container installation, please execute the following commands on windows host machine:

```
# Web UI port
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "default" natpf1 "tcp-port8080,tcp,,8080,,8080"
# MQTT port
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "default" natpf1 "tcp-port1883,tcp,,1883,,1883"
# CoAP port
"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" controlvm "default" natpf1 "tcp-port5683,tcp,,5683,,5683"
```

- Now you should be able to open Web UI using following link:

```
http://localhost:8080/
```

### Advanced usage

See corresponding page in [linux guide](https://github.com/thingsboard/thingsboard.github.io/blob/master/docs/user-guide/install/docker/#advanced-usage) for more details.

### Troubleshooting

#### DNS issues

**Note** If you observe errors related to DNS issues, for example

```
127.0.1.1:53: cannot unmarshal DNS message
```

You may configure your system to use [Google public DNS servers](https://developers.google.com/speed/public-dns/docs/using#windows)

## Installing ThingsBoard on Centos

### Third-party components installation

#### Java 

install Oracle JDK 8

```
cd ~
wget --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jre-8u144-linux-x64.rpm
```

The latest revision can be found from [Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html).

```
sudo yum localinstall jre-8u144-linux-x64.rpm
```

Now Java should be installed at`/usr/java/jdk1.8.0_60/jre/bin/java`, and linked from`/usr/bin/java`.

You may delete the archive file that you downloaded earlier:

```
rm jre-8u144-linux-x64.rpm
```

Using the `java -version` command, users can confirm the default \(recently installed\) JDK version. Result should as below:

```
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)
```

#### \[Optional\] SQL Database: PostgreSQL

```
# Update your system
sudo yum update
# Install packages
sudo yum install postgresql-server postgresql-contrib
# Initialize your PostgreSQL DB
sudo postgresql-setup initdb
sudo systemctl start postgresql
# Optional: Configure PostgreSQL to start on boot
sudo systemctl enable postgresql
```

See the following guides for more details:[using postgresql roles and databases](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-postgresql-on-ubuntu-16-04#using-postgresql-roles-and-databases)and[changing the postgres user password](https://blog.2ndquadrant.com/how-to-safely-change-the-postgres-user-password-via-psql/)

When it’s done, connect to the database and create thingsboard DB:

```
psql -U postgres -d postgres -h 127.0.0.1 -W

CREATE DATABASE thingsboard;
\q
```

### ThingsBoard service installation {#thingsboard-service-installation}

```
wget https://github.com/thingsboard/thingsboard/releases/download/v1.3/thingsboard-1.3.rpm
sudo rpm -Uvh thingsboard-1.3.rpm
```

#### \[Optional\] Configure ThingsBoard to use the external database {#optional-configure-thingsboard-to-use-the-external-database}

**NOTE:**This is an**optional**step. It is required only for production usage. You can use embedded HSQLDB for platform evaluation or development

Edit ThingsBoard configuration file

```
sudo nano /etc/thingsboard/conf/thingsboard.yml
```

Comment ‘\# HSQLDB DAO Configuration’ block.

```
# HSQLDB DAO Configuration
#spring:
#  data:
#    jpa:
#      repositories:
#        enabled: "true"
#  jpa:
#    hibernate:
#      ddl-auto: "validate"
#    database-platform: "org.hibernate.dialect.HSQLDialect"
#  datasource:
#    driverClassName: "${SPRING_DRIVER_CLASS_NAME:org.hsqldb.jdbc.JDBCDriver}"
#    url: "${SPRING_DATASOURCE_URL:jdbc:hsqldb:file:${SQL_DATA_FOLDER:/tmp}/thingsboardDb;sql.enforce_size=false}"
#    username: "${SPRING_DATASOURCE_USERNAME:sa}"
#    password: "${SPRING_DATASOURCE_PASSWORD:}"
```

For**PostgreSQL**:

Uncomment ‘\# PostgreSQL DAO Configuration’ block.

```
# PostgreSQL DAO Configuration
spring:
  data:
    jpa:
      repositories:
        enabled: "true"
  jpa:
    hibernate:
      ddl-auto: "validate"
    database-platform: "org.hibernate.dialect.PostgreSQLDialect"
  datasource:
    driverClassName: "${SPRING_DRIVER_CLASS_NAME:org.postgresql.Driver}"
    url: "${SPRING_DATASOURCE_URL:jdbc:postgresql://localhost:5432/thingsboard}"
    username: "${SPRING_DATASOURCE_USERNAME:postgres}"
    password: "${SPRING_DATASOURCE_PASSWORD:postgres}"
```

For ThingsBoard service, update ThingsBoard memory usage and restrict it to 256MB in /etc/thingsboard/conf/thingsboard.conf

```
export JAVA_OPTS="$JAVA_OPTS -Xms256M -Xmx256M"
```

### Run installation script {#run-installation-script}

Once ThingsBoard service is installed, you can execute the following script:

```
# --loadDemo option will load demo data: users, devices, assets, rules, widgets.

sudo /usr/share/thingsboard/bin/install/install.sh --loadDemo
```

### Start ThingsBoard service {#start-thingsboard-service}

Execute the following command to start ThingsBoard:

```
sudo service thingsboard start
```

Once started, you will be able to open Web UI using the following link:

```
http://localhost:8080/
```

**NOTE**: Please allow up to 90 seconds for the Web UI to start

