D-Link DSL-2750U U1 GPL ME_1.09 firmware with p910nd print server, to print via `socket://router_ip:9100`

Tested on DSL-2750E T1, should work on DSL-2730E, DSL-2730U, DSL-2740EL.

## Directions to build a firmware from source

### 1. Download original firmware

https://dlink-gpl.s3.amazonaws.com/GPL1600007/DSL-2750U_U1_GPL_ME_1.09_ME.tar(A0).gz

If this link doesn't work, search for DSL-2750U U1 GPL ME_1.09 on https://tsd.dlink.com.tw


### 2. Make a virtual machine with CentOS 5.4

Install from `CentOS-5.4-i386-bin-DVD.iso`

You will at least need those software bundles:
* Development > Development Libraries
* Development > Development Tools
* Base System > Base

The only missing lib after install is libpng-devel, you can install it with:
```sh
mount /dev/cdrom /mnt
rpm -i /mnt/CentOS/libpng-devel*
```

### 3. Build

Copy downloaded firmware archive and patch files to the `/root` dir of the virtial machine (use `scp`).

```sh
cd /root

tar xf DSL-2750U_U1_GPL_ME_1.09_ME.tar(A0).gz

mkdir -p /opt/tbs_toolchains/realtek
tar -C /opt/tbs_toolchains/realtek -jxf rsdk-1.5.6-5281-EB-2.6.30-0.9.30.3-110915.tar.bz2

tar jxf DSL2750U_U1_GPL_ME_1.09_ME.tar.bz2
patch -p1 < 0001-fix-squashfs-build-add-no-xattrs.patch
patch -p1 < 0002-reenable-ippd-manager-form-web-interface.patch
patch -p1 < 0003-add-p910nd-daemon.patch
chmod -R 777 DSL2750U_U1_GPL_ME_1.09_ME

cd DSL2750U_U1_GPL_ME_1.09_ME/RTL8676xx
make autobuild AUTOPRODUCT=DSL2750U
```

Grab you firmare binary in the build dir at `./build/romfs/image.img`

### 4. Flash a firmware in recovery mode

* Boot the router in recovery mode by holding down reset button while switching it on
* As there is no DHCP provided, assign your PC an IP address of 192.168.1.10
* Flash from Recovery UI at http://192.168.1.1
