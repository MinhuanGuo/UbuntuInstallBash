# Ubuntu 20.04.1 LTS 软件安装

[TOC]

------

## 1. Typora 编辑器

> 官网：[typora.io](https://typora.io/)

```bash
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```



## 2.台式机Nvidia驱动

> [参考教程](https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-20-04-focal-fossa-linux)

```bash
# https://linuxconfig.org/how-to-install-the-nvidia-drivers-on-ubuntu-20-04-focal-fossa-linux
sudo apt-get update
sudo apt-get upgrade

# 先安装一些依赖库
sudo apt install build-essential libglvnd-dev pkg-config

# 检查驱动版本
ubuntu-drivers devices
# 自动安装显卡驱动
sudo ubuntu-drivers autoinstall

# 重启
sudo reboot

# 有卡顿 https://www.jianshu.com/p/4cc4bcf6d2a4
cat /proc/sys/vm/swappiness   
# 应该是60

# 修改：
sudo gedit /etc/sysctl.conf
# 在文件的末尾加上 
vm.swappiness=10
```

成功安装之后，在**设置**>**关于**中可以看到**图形**一栏已经显示对应的显卡（1660 Ti）。

<img src="/home/mh/MH_TUT/UbuntuInstallBash/img/图形配置.png" alt="图形配置" style="zoom:50%;" />

## 3. 安装OpenCV

> 版本：OpenCV4.2.0
>
> 官方教程：[**Installation in Linux**](https://docs.opencv.org/4.2.0/d7/d9f/tutorial_linux_install.html)

- 第一步：更新系统

  ```bash
  sudo apt-get -y update
  sudo apt-get -y upgrade       # Uncomment to install new versions of packages currently installed
  sudo apt-get -y dist-upgrade  # Uncomment to handle changing dependencies with new vers. of pack.
  sudo apt-get -y autoremove    # Uncomment to remove packages that are now no longer needed
  ```


- 第二步：安装依赖项

  ```bash
  # Build tools:
  sudo apt-get install -y build-essential cmake
  
  # GUI (if you want GTK, change 'qt5-default' to 'libgtkglext1-dev' and remove '-DWITH_QT=ON'):
  sudo apt-get install -y qt5-default libvtk6-dev
  
  # Media I/O:
  sudo apt-get install -y zlib1g-dev libjpeg-dev libwebp-dev libpng-dev \
  						libtiff5-dev libjasper-dev libopenexr-dev libgdal-dev
  
  # Video I/O:
  sudo apt-get install -y libdc1394-22-dev libavcodec-dev libavformat-dev \
  						libswscale-dev libtheora-dev libvorbis-dev \ 
  						libxvidcore-dev libx264-dev yasm \
  						libopencore-amrnb-dev libopencore-amrwb-dev \
                          libv4l-dev libxine2-dev
  
  # Parallelism and linear algebra libraries:
  sudo apt-get install -y libtbb-dev libeigen3-dev
  
  # Python:
  sudo apt-get install -y python-dev  python-tk  pylint  python-numpy  \
                          python3-dev python3-tk pylint3 python3-numpy flake8
  
  # Java:
  sudo apt-get install -y ant default-jdk
  
  # Documentation and other:
  sudo apt-get install -y doxygen unzip wget
  ```

  

- 第三步：下载OpenCV 4.2.0

  ```bash
  wget https://github.com/opencv/opencv/archive/4.2.0.zip # OpenCV 4.2.0
  wget https://github.com/opencv/opencv_contrib/archive/4.2.0.zip # OpenCV_contrib 4.2.0
  unzip opencv-4.2.0.zip
  unzip opencv_contrib-4.2.0.zip
  
  mkdir -p ~/OpenCV
  mv opencv-4.2.0 ~/OpenCV
  mv opencv_contrib-4.2.0 ~/OpenCV 
  ```

- 第四步：安装OpenCV 4.2.0 

  ```bash
  cd ~/OpenCV/opencv-4.2.0
  mkdir build && cd build
  
  cmake -DWITH_QT=ON -DWITH_OPENGL=ON -DFORCE_VTK=ON -DWITH_TBB=ON -DWITH_GDAL=ON \
        -DWITH_XINE=ON -DENABLE_PRECOMPILED_HEADERS=OFF ..
  
  sudo make -j
  sudo make install
  sudo ldconfig
  ```

  

## 4. Ubuntu一些配置指令

> 来源：https://sspai.com/post/60411

### 1. 更新系统

```bash
# 更新本地包数据库
sudo apt update

# 更新所有已安装的包（也可以使用 full-upgrade）
sudo apt upgrade

# 自动移除不需要的包
sudo apt autoremove
```

### 2. 安装 Git

```bash
apt install git
```

### 3. JetBrains 全家桶

安装 JetBrains 的 [ToolBox App](https://www.jetbrains.com/toolbox-app/) 后可以无脑一键安装旗下各种 IDE

### 4. 桌面美化

- 安装 tweek

  ```bash
  sudo apt install gnome-tweak-tool
  ```

### 5. Windows、Ubuntu 双系统时间不统一

如果你是双系统，安装完 Ubuntu 设置好系统时间后，回到 Windows 会发现时间不统一。为了理解为什么，我们得先了解点基础知识：

- UTC(Coordinated Universal Time)，协调世界时（世界统一时间)；
- GMT(Greenwich Mean Time)，格林威治标准时间。

Windows 与类 Unix 系统(Unix/Linux/Mac)看待系统硬件时间的方式是不一样的：

- Windows 把计算机硬件时间当作本地时间(local time)，所以在 Windows 系统中显示的时间跟 BIOS 中显示的时间是一样的。
- 类 Unix 系统把计算机硬件时间当作 UTC， 所以系统启动后会在该时间的基础上，加上电脑设置的时区数(比中国就加8)，因此 Ubuntu 中显示的时间总是比 Windows 中显示的时间快 8 小时。

当你在 Ubuntu 中把系统显示的时间设置正确后，计算机硬件时间就变成了在这个时间上减去 8 小时，所以当你切换成 Windows 系统后慢了8小时，就是这个原因。

**解决方案**：在 Ubuntu 中把计算机硬件的时间改成系统显示时间，即禁用 Ubuntu 中的 UTC

```bash
timedatectl set-local-rtc 1 --adjust-system-clock
```

### 6. 命令行工具

- 安装Terminator

  如果你用惯了 Mac 平台下的 iTerm2，一定会对它的分屏功能恋恋不忘，然而 Ubuntu 自带的 Terminal 并不好用。好在 Linux 下有各种开源 Terminal，个人比较推荐 Terminator，强大如 iTerm2，一样支持**分屏**。

  ```bash
  # 安装
  sudo add-apt-repository ppa:gnome-terminator
  sudo apt update
  sudo apt install terminator
  ```

  Terminator 默认的界面比较丑，不过配置灵活，大家可以根据喜好自行调整。

  > 配置方法：
  >
  > ```bash
  > sudo gedit   ~/.config/terminator/config
  > ```
  >
  > 将配置文件修改为如下内容：
  >
  > ```shell
  > [global_config]
  > [keybindings]
  > [profiles]
  >   [[default]]
  >     use_system_font = False # 是否启用系统字体
  >     login_shell = True
  >     background_darkness = 0.92 # 背景颜色
  >     background_type = transparent
  >     background_image = None
  >     cursor_color = "#3036ec" # 光标颜色
  >     foreground_color = "#00ff00"
  >     show_titlebar = False # 不显示标题栏，也就是 terminator 中那个默认的红色的标题栏
  >     custom_command = tmux
  >     font = Ubuntu Mono 16  # 字体设置，后面的数字表示字体大小
  > [layouts]
  >   [[default]]
  >     [[[child1]]]
  >       type = Terminal
  >       parent = window0
  >     [[[window0]]]
  >       type = Window
  >       parent = ""
  > [plugins]
  > ```

- 在Ubuntu 20.04中设置**右键打开terminator**而非系统terminal

  > 教程：https://blog.csdn.net/qq_42703283/article/details/106590786

  1. 假设已经安装好了terminator了

  2. 安装nautilus-actions filemanager-actions

     ```bash
     sudo apt-get install nautilus-actions filemanager-actions
     ```

  3. 运行filemanager-actions

     ```bash
     fma-config-tool
     ```

     新建动作，配置如下：

     ![terminator001](/home/mh/MH_TUT/UbuntuInstallBash/img/terminator001.png)

     动作如下：

     ![terminator002](/home/mh/MH_TUT/UbuntuInstallBash/img/terminator002.png)

     **路径**填入：

     `/usr/bin/terminator`

     **参数**填入：

     `--working-directory=%d/%b`

     左上角**Filemanager…tool**打开**Preference**，设置如下

     ![terminator003](/home/mh/MH_TUT/UbuntuInstallBash/img/terminator003.png)

  4. 保存，然后命令行输入

     ```bash
     nautilus -q
     ```

- 配置 Shell（安装 zsh 和 oh-my-zsh）