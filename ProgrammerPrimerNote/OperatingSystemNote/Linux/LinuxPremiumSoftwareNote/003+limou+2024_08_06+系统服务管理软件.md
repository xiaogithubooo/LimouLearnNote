# 1.systemctl 基础命令

`systemctl` 是一个用于管理和控制 `systemd` 系统和服务管理器的命令行工具（其实就是管理系统资源的工具）。在现代 `Linux` 发行版中，`systemd` 作为系统初始化和服务管理系统，被广泛使用。`systemctl` 允许用户启动、停止、重新加载和管理系统服务及其相关进程。

在较老旧的系统管理中，常常使用 `init` 进程来启动服务，这种启动很快捷，但是不够灵活和强大，这里举一个阿帕奇的例子。

```shell
$ sudo /etc/init.d/apache2 start # 或 service apache2 start
```

而这个 `init.d` 内部存储的其实就是脚本，您可以简单看一下内容。

```shell
# 查看 /etc/init.d/apache2 文件内部的脚本逻辑
cat /etc/init.d/apache2
#!/bin/sh
### BEGIN INIT INFO
# Provides:          apache2
# Required-Start:    $local_fs $remote_fs $network $syslog $named
# Required-Stop:     $local_fs $remote_fs $network $syslog $named
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# X-Interactive:     true
# Short-Description: Apache2 web server
# Description:       Start the web server
#  This script will start the apache2 web server.
### END INIT INFO

DESC="Apache httpd web server"
NAME=apache2
DAEMON=/usr/sbin/$NAME

SCRIPTNAME="${0##*/}"
SCRIPTNAME="${SCRIPTNAME##[KS][0-9][0-9]}"
if [ -n "$APACHE_CONFDIR" ] ; then
if [ "${APACHE_CONFDIR##/etc/apache2-}" != "${APACHE_CONFDIR}" ] ; then
DIR_SUFFIX="${APACHE_CONFDIR##/etc/apache2-}"
else
DIR_SUFFIX=
fi
elif [ "${SCRIPTNAME##apache2-}" != "$SCRIPTNAME" ] ; then
DIR_SUFFIX="-${SCRIPTNAME##apache2-}"
APACHE_CONFDIR=/etc/apache2$DIR_SUFFIX
else
DIR_SUFFIX=
APACHE_CONFDIR=/etc/apache2
fi
if [ -z "$APACHE_ENVVARS" ] ; then
APACHE_ENVVARS=$APACHE_CONFDIR/envvars
fi
export APACHE_CONFDIR APACHE_ENVVARS

ENV="env -i LANG=C PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
if [ "$APACHE_CONFDIR" != /etc/apache2 ] ; then
ENV="$ENV APACHE_CONFDIR=$APACHE_CONFDIR"
fi
if [ "$APACHE_ENVVARS" != "$APACHE_CONFDIR/envvars" ] ; then
ENV="$ENV APACHE_ENVVARS=$APACHE_ENVVARS"
fi

PIDFILE=$(. $APACHE_ENVVARS && echo $APACHE_PID_FILE)

VERBOSE=no
if [ -f /etc/default/rcS ]; then
. /etc/default/rcS
fi
. /lib/lsb/init-functions


# Now, set defaults:
APACHE2CTL="$ENV apache2ctl"
PIDFILE=$(. $APACHE_ENVVARS && echo $APACHE_PID_FILE)
APACHE2_INIT_MESSAGE=""

CONFTEST_OUTFILE=
cleanup() {
if [ -n "$CONFTEST_OUTFILE" ] ; then
rm -f "$CONFTEST_OUTFILE"
fi
}
trap cleanup 0  # "0" means "EXIT", but "EXIT" is not portable


apache_conftest() {
[ -z "$CONFTEST_OUTFILE" ] || rm -f "$CONFTEST_OUTFILE"
CONFTEST_OUTFILE=$(mktemp)
if ! $APACHE2CTL configtest > "$CONFTEST_OUTFILE" 2>&1 ; then
return 1
else
rm -f "$CONFTEST_OUTFILE"
CONFTEST_OUTFILE=
return 0
fi
}

clear_error_msg() {
[ -z "$CONFTEST_OUTFILE" ] || rm -f "$CONFTEST_OUTFILE"
CONFTEST_OUTFILE=
APACHE2_INIT_MESSAGE=
}

print_error_msg() {
[ -z "$APACHE2_INIT_MESSAGE" ] || log_warning_msg "$APACHE2_INIT_MESSAGE"
if [ -n "$CONFTEST_OUTFILE" ] ; then
echo "Output of config test was:" >&2
cat "$CONFTEST_OUTFILE" >&2
rm -f "$CONFTEST_OUTFILE"
CONFTEST_OUTFILE=
fi
}

apache_wait_start() {
local STATUS=$1
local i=0

if [ $STATUS != 0 ] ; then
return $STATUS
fi
while : ; do
PIDTMP=$(pidofproc -p $PIDFILE $DAEMON)
if [ -n "${PIDTMP:-}" ] && kill -0 "${PIDTMP:-}" 2> /dev/null; then
return $STATUS
fi

if [ $i = "20" ] ; then
APACHE2_INIT_MESSAGE="The apache2$DIR_SUFFIX instance did not start within 20 seconds. Please read the log files to discover problems"
return 2
fi

[ "$VERBOSE" != no ] && log_progress_msg "."
sleep 1
i=$(($i+1))
done
}

apache_wait_stop() {
local STATUS=$1
local METH=$2

if [ $STATUS != 0 ] ; then
return $STATUS
fi

PIDTMP=$(pidofproc -p $PIDFILE $DAEMON)
if [ -n "${PIDTMP:-}" ] && kill -0 "${PIDTMP:-}" 2> /dev/null; then
if [ "$METH" = "kill" ]; then
killproc -p $PIDFILE $DAEMON
else
$APACHE2CTL $METH > /dev/null 2>&1
fi

local i=0
while kill -0 "${PIDTMP:-}" 2> /dev/null;  do
if [ $i = '60' ]; then
STATUS=2
break
fi
[ "$VERBOSE" != no ] && log_progress_msg "."
sleep 1
i=$(($i+1))
done
return $STATUS
else
return $STATUS
fi
}


#
# Function that starts the daemon/service
#
do_start()
{
# Return
#   0 if daemon has been started
#   1 if daemon was already running
#   2 if daemon could not be started

if pidofproc -p $PIDFILE "$DAEMON" > /dev/null 2>&1 ; then
return 1
fi

if apache_conftest ; then
$APACHE2CTL start
apache_wait_start $?
return $?
else
APACHE2_INIT_MESSAGE="The apache2$DIR_SUFFIX configtest failed."
return 2
fi
}

#
# Function that stops the daemon/service
#
do_stop()
{
# Return
#   0 if daemon has been stopped
#   1 if daemon was already stopped
#   2 if daemon could not be stopped
#   other if a failure occurred

# either "stop" or "graceful-stop"
local STOP=$1
# can't use pidofproc from LSB here
local AP_RET=0

if pidof $DAEMON > /dev/null 2>&1 ; then
if [ -e $PIDFILE ] && pidof $DAEMON | tr ' ' '\n' | grep -w $(cat $PIDFILE) > /dev/null 2>&1 ; then
AP_RET=2
else
AP_RET=1
fi
else
AP_RET=0
fi

# AP_RET is:
# 0 if Apache (whichever) is not running
# 1 if Apache (whichever) is running
# 2 if Apache from the PIDFILE is running

if [ $AP_RET = 0 ] ; then
return 1
fi

if [ $AP_RET = 2 ] && apache_conftest ; then
apache_wait_stop $? $STOP
return $?
else
if [ $AP_RET = 2 ]; then
clear_error_msg
APACHE2_INIT_MESSAGE="The apache2$DIR_SUFFIX configtest failed, so we are trying to kill it manually. This is almost certainly suboptimal, so please make sure your system is working as you'd expect now!"
apache_wait_stop $? "kill"
return $?
elif [ $AP_RET = 1 ] ; then
APACHE2_INIT_MESSAGE="There are processes named 'apache2' running which do not match your pid file which are left untouched in the name of safety, Please review the situation by hand".
return 2
fi
fi

}


#
# Function that sends a SIGHUP to the daemon/service
#
do_reload() {
if apache_conftest; then
if ! pidofproc -p $PIDFILE "$DAEMON" > /dev/null 2>&1 ; then
APACHE2_INIT_MESSAGE="Apache2 is not running"
return 2
fi
$APACHE2CTL graceful > /dev/null 2>&1
return $?
else
APACHE2_INIT_MESSAGE="The apache2$DIR_SUFFIX configtest failed. Not doing anything."
return 2
fi
}


# Sanity checks. They need to occur after function declarations
[ -x $DAEMON ] || exit 0

if [ ! -x $DAEMON ] ; then
echo "No apache-bin package installed"
exit 0
fi

if [ -z "$PIDFILE" ] ; then
echo ERROR: APACHE_PID_FILE needs to be defined in $APACHE_ENVVARS >&2
exit 2
fi


case "$1" in
start)
log_daemon_msg "Starting $DESC" "$NAME"
do_start
RET_STATUS=$?
case "$RET_STATUS" in
0|1)
log_success_msg
[ "$VERBOSE" != no ] && [ $RET_STATUS = 1 ] && log_warning_msg "Server was already running"
;;
2)
log_failure_msg
print_error_msg
exit 1
;;
esac
;;
stop|graceful-stop)
log_daemon_msg "Stopping $DESC" "$NAME"
do_stop "$1"
RET_STATUS=$?
case "$RET_STATUS" in
0|1)
log_success_msg
[ "$VERBOSE" != no ] && [ $RET_STATUS = 1 ] && log_warning_msg "Server was not running"
;;
2)
log_failure_msg
print_error_msg
exit 1
;;
esac
print_error_msg

;;
status)
status_of_proc -p $PIDFILE "apache2" "$NAME"
exit $?
;;
reload|force-reload|graceful)
log_daemon_msg "Reloading $DESC" "$NAME"
do_reload
RET_STATUS=$?
case "$RET_STATUS" in
0|1)
log_success_msg
[ "$VERBOSE" != no ] && [ $RET_STATUS = 1 ] && log_warning_msg "Server was already running"
;;
2)
log_failure_msg
print_error_msg
exit 1
;;
esac
print_error_msg
;;
restart)
log_daemon_msg "Restarting $DESC" "$NAME"
do_stop stop
case "$?" in
0|1)
do_start
case "$?" in
0)
log_end_msg 0
;;
1|*)
log_end_msg 1 # Old process is still or failed to running
print_error_msg
exit 1
;;
esac
;;
*)
# Failed to stop
log_end_msg 1
print_error_msg
exit 1
;;
esac
;;
start-htcacheclean|stop-htcacheclean)
echo "Use 'service apache-htcacheclean' instead"
;;
*)
echo "Usage: $SCRIPTNAME {start|stop|graceful-stop|restart|reload|force-reload}" >&2
exit 3
;;
esac

exit 0

# vim: syntax=sh ts=4 sw=4 sts=4 sr noet
```

其实不用看也会发现，要靠这种配置脚本管理服务实在是太慢了，并且使用服务的用户一旦需要定制化，就需要修改配置文件，因此后来就诞生了 `Systemd` 这种服务管理软件，从名字上来看就是 `System+d` 这里的 `d` 就是守护进程的意思，暗含守护整个系统之意，它的作者也是一位有趣的人，叫 `Lennart Poettering`，您可以 [前去 wiki 上翻阅他的经历](https://en.wikipedia.org/wiki/Lennart_Poettering)，以及关于 `systemd` 的使用争论。

而有了 `systemd` 就不再需要 `init` 了，我们可以看到自从使用了 `systemd` 后 `pid=1` 的进程就不再是 `init` 了，因此我们需要知道至少一点，`init` 该有的功能，理论上 `systemd` 也应该有，并且更加强大。

```shell
# 查看 PID 为 1 的进程名字
ps -p 1 -o comm=
systemd
# ps -p 1: 显示 PID 为 1 的进程
# -o comm=: 仅输出进程的名称, 不包括标题行
```

而作为管理软件，无非就是增删查改，但是 `systemd` 实际上不是一个命令而是一组命令，可以看看与之相关的指令：

-   重启系统 `sudo systemctl reboot`
-   关闭系统，切断电源 `sudo systemctl poweroff`
-   `CPU` 停止工作 `sudo systemctl halt`
-   暂停系统 `sudo systemctl suspend`
-   让系统进入冬眠状态 `sudo systemctl hibernate`
-   让系统进入交互式休眠状态 `sudo systemctl hybrid-sleep`
-   启动进入救援状（单用户状态） `sudo systemctl rescue`
-   ...

上面是和操作系统本身服务有关的指令，除此以外还有很多很多，但是我们今天的重点其实反倒不是上面这些，而是把我们自己的用户进程变成服务，因此我们重点关注以下的命令。

-   查看服务状态 `sudo systemctl status <service_name>`
-   启动服务 `sudo systemctl start <service_name>`
-   停止服务 `sudo systemctl stop <service_name>`
-   杀死服务 `sudo systemctl kill <service_name>`
-   重启服务 `sudo systemctl restart <service_name>`
-   重新加载服务配置 `sudo systemctl reload <service_name>`
-   启用服务开机自启动 `sudo systemctl enable <service_name>`
-   禁用服务开机自启动 `sudo systemctl disable <service_name>`
-   列出所有服务的状态 `systemctl list-units --type=service`
-   查看系统的启动状态 `systemctl is-system-running`

# 2.systemctl 编写配置

但我们如何让自己的进程变成服务来交给 `systemctl ` 管理呢？

## 2.1.确保你有一个服务文件

`systemctl` 管理的服务通常需要一个服务单元文件，这个文件通常位于 `/etc/systemd/system/` 但是里面大部分都是指向 `usr/lib/systemd/system/` 目录下，配置文件扩展名是 `.service`。这个服务文件定义了服务的启动、停止、重启...行为，一个基本的服务单元（对于 `Systemd` 来说一个服务会被看做一个单元 `Unit`）文件示例如下。

```ini
# 配置文件
[Unit]
Description=My Sample Service # 对本服务的描述, 这个字段在服务管理工具查阅 status 中显示

[Service]
ExecStart=/usr/bin/my_program --option # 定义服务启动时需要执行的命令
Restart=always # 定义服务重启的策略, 这里的 always 关键字代表无论服务因任何原因停止, systemd 都会自动重新启动该服务, 也有一些其他策略

[Install]
WantedBy=multi-user.target # 定义了服务应该在何时启动的策略, multi-user.target 表示服务在系统启动到多用户模式时启动, 通常是在系统启动完成并准备好用户登录的阶段, 也有一些其他策略
```

>   补充：这里给出一个 `redis` 自己的配置例子，您可以简单查阅一下。
>
>   ```ini
>   # 如果您有安装 redis, 请查看 /etc/systemd/system/redis.service -> ...
>   [Unit]
>   Description=Advanced key-value store
>   After=network.target
>   Documentation=http://redis.io/documentation, man:redis-server(1)
>   
>   [Service]
>   Type=notify
>   ExecStart=/usr/bin/redis-server /etc/redis/redis.conf --supervised systemd --daemonize no
>   PIDFile=/run/redis/redis-server.pid
>   TimeoutStopSec=0
>   Restart=always
>   User=redis
>   Group=redis
>   RuntimeDirectory=redis
>   RuntimeDirectoryMode=2755
>   
>   UMask=007
>   PrivateTmp=yes
>   LimitNOFILE=65535
>   PrivateDevices=yes
>   ProtectHome=yes
>   ReadOnlyDirectories=/
>   ReadWritePaths=-/var/lib/redis
>   ReadWritePaths=-/var/log/redis
>   ReadWritePaths=-/var/run/redis
>   
>   NoNewPrivileges=true
>   CapabilityBoundingSet=CAP_SETGID CAP_SETUID CAP_SYS_RESOURCE
>   MemoryDenyWriteExecute=true
>   ProtectKernelModules=true
>   ProtectKernelTunables=true
>   ProtectControlGroups=true
>   RestrictRealtime=true
>   RestrictNamespaces=true
>   RestrictAddressFamilies=AF_INET AF_INET6 AF_UNIX
>   
>   # redis-server can write to its own config file when in cluster mode so we
>   # permit writing there by default. If you are not using this feature, it is
>   # recommended that you replace the following lines with "ProtectSystem=full".
>   ProtectSystem=true
>   ReadWriteDirectories=-/etc/redis
>   
>   [Install]
>   WantedBy=multi-user.target
>   Alias=redis.service
>   ```

不过这个软链接映射的过程我们一般不会自己手动来做，而是在 `/etc/systemd/system/` 目录下使用 `systemctl enable xxx.service `，这种设置会导致，如果配置文件中设置了开机启动，就会激活这个配置选项。因此如果对于配置文件中带有开机启动的配置选项，可以使用 `sudo systemctl disable xxx.service` 来取消开机启动。

## 2.2.将服务单元文件放置在合适的位置

将您的服务单元文件放置在 `/etc/systemd/system/` 目录下（如果是系统范围的服务），或在用户范围的目录下（如 `$HOME/.config/systemd/user/`），将服务单元文件放置在不同的目录下会影响服务的范围和管理方式，我们这里只演示后者。

1.   **全局服务** 的服务单元文件是系统范围的，适用于所有用户和系统的全局服务。它们在系统启动时由 `systemd` 管理。需要超级用户（root）权限来创建、修改或删除这些文件，适用于系统管理员或需要在所有用户会话中运行的服务，通常优先级更高。
2.   **用户服务** 这些服务单元文件是用户范围的，只对当前用户有效。它们在用户登录时由 `systemd` 的用户实例管理。可以在没有超级用户权限的情况下创建、修改或删除这些文件。这些服务通常与用户会话有关，如用户级别的应用程序、任务调度等。用户服务不会在系统启动时自动启动，而是在用户登录后启动。你需要通过 `systemctl --user` 来管理这些服务。

### 使用示例

#### 系统范围服务示例

假设你有一个名为 `my-service.service` 的服务单元文件，你可以将它放在 `/etc/systemd/system/` 中：

```sh
sudo cp my-service.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable my-service.service
sudo systemctl start my-service.service
```

#### 用户范围服务示例

如果你希望创建一个用户范围的服务，可以将单元文件放在 `$HOME/.config/systemd/user/` 中：

```sh
mkdir -p $HOME/.config/systemd/user
cp my-user-service.service $HOME/.config/systemd/user/
systemctl --user daemon-reload
systemctl --user enable my-user-service.service
systemctl --user start my-user-service.service
```

### 总结

- **`/etc/systemd/system/`** 适用于需要全局管理的服务，并且需要超级用户权限。
- **`$HOME/.config/systemd/user/`** 适用于用户级别的服务，不需要超级用户权限，并且服务只在用户登录时运行。

### 3. 重新加载 `systemd` 配置

每次创建或修改服务单元文件后，你需要重新加载 `systemd` 配置：

```sh
sudo systemctl daemon-reload
```

### 4. 启动服务

使用 `systemctl start` 命令启动服务：

```sh
sudo systemctl start my-service.service
```

这里 `my-service.service` 是你的服务单元文件的名称（去掉 `.service` 后缀）。

### 5. 设置开机自启动（可选）

如果你希望服务在系统启动时自动启动，可以使用 `systemctl enable` 命令：

```sh
sudo systemctl enable my-service.service
```

### 6. 检查服务状态

你可以使用 `systemctl status` 命令查看服务的当前状态和日志：

```sh
sudo systemctl status my-service.service
```

### 7. 停止服务

如果你需要停止服务，可以使用 `systemctl stop` 命令：

```sh
sudo systemctl stop my-service.service
```

### 示例

假设你有一个名为 `my_program` 的可执行文件，你希望它在后台运行，并且在系统启动时自动启动。你可以创建一个名为 `my-program.service` 的服务单元文件，内容如下：

```ini
[Unit]
Description=My Program

[Service]
ExecStart=/usr/bin/my_program
Restart=always

[Install]
WantedBy=multi-user.target
```

将这个文件保存为 `/etc/systemd/system/my-program.service`，然后执行以下命令：

```sh
sudo systemctl daemon-reload
sudo systemctl start my-program.service
sudo systemctl enable my-program.service
```

这样，`my_program` 就会作为一个服务启动，并且在系统启动时自动运行。