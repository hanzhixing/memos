[返回首页](../README.md)

# UUID的使用

## UUID的背景
可以看一下[wikipedia上的Universally unique identifier](https://en.wikipedia.org/wiki/Universally_unique_identifier)。
其中也描述了UUID的重复可能性。

## UUID用途
Linux比较显而易见的是作为存储设备的唯一标识，在/etc/fstab文件中可以查看到。我的如下。
```bash
$ cat /etc/fstab | grep 'UUID'
UUID=c3a34d49-e30f-46e4-b6b8-eccb0427e7e7  /boot  ext2  defaults  0  2
```

生成它的源码路径是:
[https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/drivers/char/random.c](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/drivers/char/random.c)。
只要你需要随机的并且全球唯一的标识，你都可以考虑用它。

## 生成UUID的方法
1. Linux自带的proc系统。
```bash
$ cat /proc/sys/kernel/random/uuid
967f5632-df74-4b5b-b6b5-1bc24e82910a
```

2. OSSP uuid
```bash
$ sudo apt-get insall uuid
$ uuid -v 4
778d1f63-86ac-4c30-a779-dfb7bf51c4b5
```
3. libuuid
```bash
$ apt-get insall uuid-runtime
$ uuid-runtime
64a2b630-05ed-4e54-9cab-d56836c34114
```
4. MacOS
```
$ uuidgen | tr "[:upper:]" "[:lower:]"
```

## Linux下查看设备的UUID
1. Linux下设备目录中查询
```bash
$ ls -l /dev/disk/by-uuid/
lrwxrwxrwx 1 root root 10 Dec  5 19:45 2f607b0f-bf61-4bfd-bb68-b800ba0c88a3 -> ../../dm-0
lrwxrwxrwx 1 root root 10 Dec  5 19:45 75673451-9694-42c9-8f89-c41b4da1e822 -> ../../dm-1
lrwxrwxrwx 1 root root 10 Dec  5 19:45 c3a34d49-e30f-46e4-b6b8-eccb0427e7e7 -> ../../sda1
```

2. Linux自带的udevadm
```bash
$ udevadm info -q all -n /dev/sda1
```
输出内容比较多。

3. Linux自带libblkid
```bash
$ /sbin/blkid /dev/sda1
/dev/sda1: UUID="c3a34d49-e30f-46e4-b6b8-eccb0427e7e7" TYPE="ext2" PARTUUID="1d89723f-01"
```

4. hwinfo
```bash
$ apt-get install hwinfo
$ /usr/sbin/hwinfo
```
输出内容比较多。
