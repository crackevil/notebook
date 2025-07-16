* basic setup

  * 安装好后update到最新版

    `wsl --update`

  * 更新后查看版本

    `wsl --version`

	* a typical version

	  ```
	  WSL 版本： 2.4.11.0
      内核版本： 5.15.167.4-1
      WSLg 版本： 1.0.65
      MSRDC 版本： 1.2.5716
      Direct3D 版本： 1.611.1-81528511
      DXCore 版本： 10.0.26100.1-240331-1435.ge-release
      Windows 版本： 10.0.19045.5487
	  ```

	* another typical version

	  ```
	  WSL 版本: 2.5.9.0
      内核版本: 6.6.87.2-1
      WSLg 版本: 1.0.66
      MSRDC 版本: 1.2.6074
      Direct3D 版本: 1.611.1-81528511
      DXCore 版本: 10.0.26100.1-240331-1435.ge-release
      Windows: 10.0.19045.6093
	  ```

  * 默认启用wsl2

    `wsl --set-default-version 2`
    `wsl --set-version <distribution name> 2`

  * modify .wslconfig

    ```
    swap=2GB
    ```

	check if swap active

	`cat /proc/swaps`

  * if linux path env confused by windows'

    add below to /etc/wsl.conf

    ```
    [interop]
    enabled=false
    appendWindowsPath=false
    ```

  * change apt mirror

  * install apts

    `sudo apt install apt-transport-https ca-certificates`
	`sudo apt install net-tools`
	`sudo apt install file`
	`sudo apt install strace`
	`sudo apt install parted`

* storage

  2 ways of mount virtual disks, vhd / raw image

  vhd has much better r/w speed than raw image. (tested by dd command, 40MB/s vs 2GB/s)

  test write speed (write first, then read the file)

  `dd if=/dev/zero of=./testfile bs=1M count=1024 oflag=direct status=progress`

  test read speed

  ```
  sudo sh -c "sync && echo 3 > /proc/sys/vm/drop_caches"
  dd if=./testfile of=/dev/null bs=1M status=progress
  ```

  * mount raw ext4 image inside linux

    `sudo mount -o loop /mnt/d/debian.bin /mnt/debian/`
	`sudo chmod 777 /mnt/debian/`

	or add to fstab (some fail message but systemd will generate a well script)

	`/mnt/d/debian.bin     /mnt/debian/    ext4    loop    0   0`

	or /etc/wsl.conf add command in [boot] section

	`command = mount -o loop /mnt/d/debian.bin /mnt/debian/`

  * mount a vhdx using wsl --mount

    * create a vhdx disk image in windows host(磁盘管理器或者hyper-v管理器)

	  不要初始化！

	  不要初始化！

	  不要初始化！

      当然实在初始化之后也可以用parted修改分区表，在mount时加上相应选项

    * create file system

      `wsl --mount xxxxx.vhdx --bare`

	  then inside linux

	  `lsblk` could see a corresponding device likes /dev/sdd

	  then run `sudo mke2fs /dev/xxx`. then exit to windows host run `wsl --unmount xxxxx.vhdx`

	* real mount the vhd

	  run `wsl --mount --vhd xxxxx.vhdx --name xxxx`, then the output shows the mount point

* network

  * export windows hosts ip as env(nat network)

    ```
	export win10=`ip route show | grep -i default | awk '{ print $3}'`
	```

    if need proxy(be careful, proxy would block localhost visit)

    ```
	export proxy_str="http://$win10:6500"
	git config --global http.proxy "$proxy_str"
	git config --global https.proxy "$proxy_str"
	export http_proxy="$proxy_str"
	export https_proxy="$proxy_str"
	```

* links

  https://unix.stackexchange.com/questions/308646/resizing-a-virtual-hard-drive

  https://learn.microsoft.com/en-us/windows/wsl/wsl-config






