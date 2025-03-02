---
title: "Armbianのインストール"
emoji: "😀"
type: "tech"
topics: [RaspberryPi,Armbian,orangepi,Wyse3040]
published: false
---
# はじめに
![P_20230511_001757.jpg](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/30586439-d8b5-6625-7cfc-e259381999ef.jpeg)

Raspberry Pi,Orangi Pi,NanoPiなどのARM搭載のsingle-board computer(SBC)はもちろんですが低スペックのAtomのx86 PCにLinuxをインストールするのにコンパクトなものを選びたいと思ったときArmbianがよいのではと最近思ってます。サーバーやIoT用の軽量Linuxです。

これだけのボードに対応しており、X86対応版もあります。

https://www.armbian.com/download/?device_support=Supported

https://www.armbian.com/uefi-x86/

X86は5種類ありMinimal CLIは一部のパッケージが無いようです。

1.Gnome desktop
2.Cinnamon desktop
3.XFCE desktop
4.CLI
5.Minimal CLI

https://www.armbian.com/faq/what-is-missing-in-minimal-diet-image/

# インストール
落としてきたイメージをUSBImagerでUSBメモリ,SD等に書き込む(拡張子xzのまま書き込み可)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/49facbcb-9938-3aa2-6c32-81b7fe597e11.png)

https://www.youtube.com/watch?v=hFrdyLc4g50

https://gitlab.com/bztsrc/usbimager/

USBメモリやSDからブートしてセットアップ開始、画面出力が可能なデバイスであれば直接操作もできますが最初からSSHが有効なのでキーボード、ディスプレイを接続しなくても別PCから操作できます。
DHCP自動取得なのでどのIPが割り当てられたかはIP Scanner等で探して接続します。

https://angryip.org/

ID:root
PASS:1234　
が初期のためこれで接続してパスワードはメッセージに従って再設定します。
![2023-05-10 13 09 15.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/8e146ee9-8d44-9476-a140-742f9b88e1fd.png)

```bash
Last login: Wed May 10 04:07:15 2023

Waiting for system to finish booting ...
/usr/lib/armbian/armbian-firstlogin: line 325: /sys/class/graphics/fb0/virtual_size: No such file or directory

Welcome to ARMBIAN!

Documentation: https://docs.armbian.com | Community: https://forum.armbian.com

IP address: 192.168.0.***

Create root password: *******
Repeat root password: *******

Choose default system command shell:

1) bash
2) zsh

Shell: BASH

Creating a new user account. Press <Ctrl-C> to abort

Please provide a username (eg. your first name): *********
Create user (*******) password: *********
Repeat user (*******) password: *********

Please provide your real name: *********

Dear Yoshinobu, your account yoshinobu has been created and is sudo enabled.
Please use this account for your daily work from now on.

Detected timezone: Asia/Tokyo

Set user language based on your location? [Y/n] Y

Generating locales: ja_JP.UTF-8
root@uefi-x86:~#
```
USBやSDからブートして別のディスクに書き込む場合は、sudo armbian-install で実行可能です。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/cf733985-f06b-7d50-232e-4f359ce0af26.png)

# 設定

## アップデート方法
```bash
sudo apt update
sudo apt upgrade
```

## WIFI設定
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/dd8f52f7-de7a-d8d4-436b-970e3c7abffb.png)
```bash
sudo nmtui-connect
```

## IP設定
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/a797156b-85fd-5901-89eb-d2aba31faae0.png)
```bash
sudo nmtui
```

## 設定メニュー

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/df09a62a-122a-621b-3a0d-b6f7798c48b9.png)

```bash
sudo armbian-config
```

https://docs.armbian.com/User-Guide_Armbian-Config/

https://github.com/armbian/config

## 文字化けするときの日本語フォント
CLIではなくてDesktop版を使うとGUIなのはいいですが文字化け全開です。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/94a3177a-11bb-c36a-4399-02027bb65efa.png)

```bash
sudo apt install fonts-vlgothic
```

## armbianmonitor

armbianmonitor -r でインストール、http://***.***.***.***:8888 でWebでモニタリングできます

```bash
root@orangepi-r1plus-lts:~# armbianmonitor -r
Installing RPi-Monitor. This can take up to 5 minutes. Be patient please
Selecting previously unselected package libdbi1:arm64.
(Reading database ... 42058 files and directories currently installed.)
Preparing to unpack .../00-libdbi1_0.9.0-6_arm64.deb ...
Unpacking libdbi1:arm64 (0.9.0-6) ...
Selecting previously unselected package libencode-locale-perl.
Preparing to unpack .../01-libencode-locale-perl_1.05-1.1_all.deb ...
Unpacking libencode-locale-perl (1.05-1.1) ...
Selecting previously unselected package libfile-which-perl.
Preparing to unpack .../02-libfile-which-perl_1.23-1_all.deb ...
Unpacking libfile-which-perl (1.23-1) ...
Selecting previously unselected package libtimedate-perl.
Preparing to unpack .../03-libtimedate-perl_2.3300-2_all.deb ...
Unpacking libtimedate-perl (2.3300-2) ...
Selecting previously unselected package libhttp-date-perl.
Preparing to unpack .../04-libhttp-date-perl_6.05-1_all.deb ...
Unpacking libhttp-date-perl (6.05-1) ...
Selecting previously unselected package libio-html-perl.
Preparing to unpack .../05-libio-html-perl_1.004-2_all.deb ...
Unpacking libio-html-perl (1.004-2) ...
Selecting previously unselected package liblwp-mediatypes-perl.
Preparing to unpack .../06-liblwp-mediatypes-perl_6.04-1_all.deb ...
Unpacking liblwp-mediatypes-perl (6.04-1) ...
Selecting previously unselected package liburi-perl.
Preparing to unpack .../07-liburi-perl_5.08-1_all.deb ...
Unpacking liburi-perl (5.08-1) ...
Selecting previously unselected package libhttp-message-perl.
Preparing to unpack .../08-libhttp-message-perl_6.28-1_all.deb ...
Unpacking libhttp-message-perl (6.28-1) ...
Selecting previously unselected package libhttp-daemon-perl.
Preparing to unpack .../09-libhttp-daemon-perl_6.12-1+deb11u1_all.deb ...
Unpacking libhttp-daemon-perl (6.12-1+deb11u1) ...
Selecting previously unselected package libipc-sharelite-perl.
Preparing to unpack .../10-libipc-sharelite-perl_0.17-4+b5_arm64.deb ...
Unpacking libipc-sharelite-perl (0.17-4+b5) ...
Selecting previously unselected package libjson-perl.
Preparing to unpack .../11-libjson-perl_4.03000-1_all.deb ...
Unpacking libjson-perl (4.03000-1) ...
Selecting previously unselected package librrd8:arm64.
Preparing to unpack .../12-librrd8_1.7.2-3+b7_arm64.deb ...
Unpacking librrd8:arm64 (1.7.2-3+b7) ...
Selecting previously unselected package librrds-perl:arm64.
Preparing to unpack .../13-librrds-perl_1.7.2-3+b7_arm64.deb ...
Unpacking librrds-perl:arm64 (1.7.2-3+b7) ...
Selecting previously unselected package rpimonitor.
Preparing to unpack .../14-rpimonitor_2.12-r0_all.deb ...
Unpacking rpimonitor (2.12-r0) ...
Setting up libfile-which-perl (1.23-1) ...
Setting up libdbi1:arm64 (0.9.0-6) ...
Setting up libipc-sharelite-perl (0.17-4+b5) ...
Setting up liblwp-mediatypes-perl (6.04-1) ...
Setting up libencode-locale-perl (1.05-1.1) ...
Setting up libio-html-perl (1.004-2) ...
Setting up libtimedate-perl (2.3300-2) ...
Setting up libjson-perl (4.03000-1) ...
Setting up liburi-perl (5.08-1) ...
Setting up librrd8:arm64 (1.7.2-3+b7) ...
Setting up libhttp-date-perl (6.05-1) ...
Setting up librrds-perl:arm64 (1.7.2-3+b7) ...
Setting up libhttp-message-perl (6.28-1) ...
Setting up libhttp-daemon-perl (6.12-1+deb11u1) ...
Setting up rpimonitor (2.12-r0) ...
Restarting rpimonitor (via systemctl): rpimonitor.service.
Processing triggers for man-db (2.9.4-2) ...
Processing triggers for libc-bin (2.31-13+deb11u6) ...

Now you're able to enjoy RPi-Monitor at http://192.168.0.20:8888
```
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/11d1eeae-a68c-ec22-f462-2f7789a9965e.png)


# ディスクやメモリ使用量とか

## Armbian_23.02.2_Uefi-x86_bullseye_current_5.15.94.img.xz

下記は投げ売りでGETしたDELLのWyse 3040(シンクライアント)に入れた時のものです

https://akiba-pc.watch.impress.co.jp/docs/wakiba/find/1456023.html

X86 CLIバージョンを16GBのUSBメモリにインストールした場合のの場合のディスク使用量は下記のようになります。

```bash
root@uefi-x86:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            903M     0  903M   0% /dev
tmpfs           190M  5.9M  184M   4% /run
/dev/sda3        14G  3.1G   11G  23% /
tmpfs           948M     0  948M   0% /dev/shm
tmpfs           5.0M  4.0K  5.0M   1% /run/lock
/dev/zram2      931M   60K  868M   1% /tmp
/dev/sda2       253M  142K  252M   1% /boot/efi
/dev/zram1       49M  1.1M   44M   3% /var/log
tmpfs           190M     0  190M   0% /run/user/0
```

![2023-05-10 13 29 35.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/08b11530-3a46-1eb3-f885-2083454bb2a9.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/cb3f9d60-8910-63e5-dbfe-8f076f12a69c.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/ce5f1ba9-e1b6-4653-4dd5-0eb65eb6faeb.png)


## Armbian_23.02.2_Orangepi-r1plus-lts_bullseye_current_5.15.93.img.xz
Orange Pi R1 Plus LTS にインストールした場合の使用量です。SDは8GB

```bash
root@orangepi-r1plus-lts:~# df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            416M     0  416M   0% /dev
tmpfs            98M  1.9M   96M   2% /run
/dev/mmcblk0p1  7.0G  1.6G  5.3G  23% /
tmpfs           489M     0  489M   0% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           489M     0  489M   0% /tmp
/dev/zram1       49M  1.6M   44M   4% /var/log
tmpfs            98M     0   98M   0% /run/user/0
```

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/fc47aeef-02e5-26f5-676f-860c8eed99de.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/cc2145dc-e376-d930-a81f-bfbdbcde1e46.png)

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/2146151/d5dc2dfd-a4e9-cd8a-39fd-415913a61ffb.png)


# 参考

https://denor.jp/armbian%E3%81%AE%E5%88%9D%E6%9C%9F%E8%A8%AD%E5%AE%9A-%E4%BB%A4%E5%92%8C5%E5%B9%B42023%E5%B9%B44%E6%9C%88%E7%89%88

https://www.mikan-tech.net/entry/nanopi-neo-setup

https://www.reddit.com/r/Armbian/

https://www.reddit.com/r/ArmbianUsers/

https://forum.armbian.com/


