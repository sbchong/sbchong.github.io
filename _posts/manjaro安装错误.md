# manjaro安装错误

新装了Manjaro KDE系统，安装某些软件时，总是出错，大致是以下两个错误

### ERROR: Cannot find the fakeroot binary required for building as non-root user.
### ERROR: Cannot find the strip binary required for object file stripping.

没安装 fakeroot、binutils 等打包基本工具,pacman -S base-devel 装一下。

