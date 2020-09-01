---
title: after openwrt sysupgrade
date: 2013-09-02T15:44:15+08:00
tags: [sysupgrade, openwrt, mw4530r]
---

每次升级Openwrt后，之前装的ipk就没有了（现在用的是Openwrt官方的bin）。这个让我很苦恼。最主要的是像我我这样的手贱版本党。有时候会远程通过web或者ssh升级系统。这样升级后由于没有了ddns模块，我就对我的路由失去控制权了。需要回到家才能配置好。

然后我就写了个脚本，每次升级后，自动安装需要的软件，把服务启动起来。这样每次sysupgrade也就只剩下一条命令的事情了。

<!--more-->

```shell
#!/bin/sh

LOCK_DIR=/opt/var/lock
LOG_DIR=/opt/var/log

LOCK_FILE=$LOCK_DIR/first_boot.lock

LOG_FILE=$LOG_DIR/after-sysupgrede.log

if [ -d $LOCK_DIR ]; then
    mkdir -p $LOCK_DIR
fi

if [ -d $LOG_DIR ]; then
    mkdir -p $LOG_DIR
fi

INSTALL_IPK="kmod-usb-audio\
    luci\
    luci-app-ddns\
    luci-app-qos\
    luci-app-upnp\
    luci-i18n-chinese"

SERVICE="dbus avahi-daemon shairport"


if [[ -f $LOCK_FILE ]]; then
    exit 0
fi

opkg update >> $LOG_FILE

if [[ $? -ne 0 ]]; then
    echo "update failed" >> $LOG_FILE
    exit 1
fi

opkg install $INSTALL_IPK >> $LOG_FILE

opkg install /root/shairport_2013-08-28_ar71xx.ipk

for x in $SERVICE; do
    /etc/init.d/$x enable >> $LOG_FILE
    /etc/init.d/$x start >> $LOG_FILE
done


/etc/init.d/uhttpd enable
/etc/init.d/uhttpd start
/etc/init.d/dbus enable
/etc/init.d/dbus start
/etc/init.d/avahi-daemon enable
/etc/init.d/avahi-daemon start
/etc/init.d/shairport enable
/etc/init.d/shairport start

touch $LOCK_FILE
```

下载mw4530r sysupgrade bin文件并检查md5脚本:

```shell
#!/bin/sh

TMP_MD5SUMS="/tmp/openwrt-md5sums"
MW4530R_MD5SUMS="/tmp/mw4530r-md5sums"
SYSUPGRADE_BIN_FILE="openwrt-ar71xx-generic-mw4530r-v1-squashfs-sysupgrade.bin"

cd /tmp

rm $SYSUPGRADE_BIN_FILE

wget "https://downloads.openwrt.org/snapshots/trunk/ar71xx/openwrt-ar71xx-generic-mw4530r-v1-squashfs-sysupgrade.bin"

wget "https://downloads.openwrt.org/snapshots/trunk/ar71xx/md5sums" -O $TMP_MD5SUMS

cat $TMP_MD5SUMS | grep $SYSUPGRADE_BIN_FILE > $MW4530R_MD5SUMS
```

