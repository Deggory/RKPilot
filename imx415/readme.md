## 下载安装镜像（ubuntu2404）
https://github.com/Joshua-Riek/ubuntu-rockchip

点击下面链接直接下载香橙派5的
https://objects.githubusercontent.com/github-production-release-asset-2e65be/587553349/1d0ff0ce-8f30-402e-8df2-3f92337786d9?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=releaseassetproduction%2F20250525%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Date=20250525T164236Z&X-Amz-Expires=300&X-Amz-Signature=274ed7e630e3a075068cb260f54f846ca0de87f2597e8528d2494524aa51d05f&X-Amz-SignedHeaders=host&response-content-disposition=attachment%3B%20filename%3Dubuntu-24.04-preinstalled-desktop-arm64-orangepi-5.img.xz&response-content-type=application%2Foctet-stream

## 以下在香橙派5ubuntu2404环境下操作

## 先解压文件夹到home目录
```bash
mv imx415.zip ~/
cd ~
unzip imx415.zip
cd ~/imx415/
```

## 备份dtb文件
```bash
cd /usr/lib/firmware/6.1.0-1025-rockchip/device-tree/rockchip/
ls | grep rk3588s-orangepi-5
sudo cp rk3588s-orangepi-5.dtb rk3588s-orangepi-5.dtb.bak
```

## 替换dtb文件
```bash
# dts所在目录
cd /usr/lib/firmware/6.1.0-1025-rockchip/device-tree/rockchip/
# 替换总的dts，里面包含了camera的更改，目前更改占用的ov13855; 如果以后还使用ov13855,只需要把这一个文件替换回去就行
sudo cp ~/imx415/dts/rk3588s-orangepi-5.dtb ./
# imx415的dts，拷贝过去后不要更改
sudo cp ~/imx415/dts/orangepi-5-imx415-c* ./overlay/
```

## 设置u-boot
```bash
sudo vi /etc/default/u-boot

# 在最后添加以下两行，已经添加的就不要重复添加了
U_BOOT_FDT="device-tree/rockchip/rk3588s-orangepi-5.dtb"
U_BOOT_FDT_OVERLAYS="device-tree/rockchip/overlay/orangepi-5-imx415-c1.dtbo"

# 如果也有wifi或其他模块，则空格分割，添加在同一项
U_BOOT_FDT_OVERLAYS="device-tree/rockchip/overlay/orangepi-5-ap6275p.dtbo device-tree/rockchip/overlay/orangepi-5-imx415-c1.dtbo"

```

## 刷新重启
```bash
sudo u-boot-update
sudo reboot
```

## 将imx415插到cam1接口，开机查看调试debug信息
```bash
sudo dmesg | grep imx415

# 看到如下信息就成功了
mx@mx-pi:~$ sudo dmesg | grep imx415
[   17.665535] imx415 7-001a: driver version: 00.01.08
[   17.665541] imx415 7-001a:  Get hdr mode failed! no hdr default
[   17.665548] imx415 7-001a: detect imx415 lane 4
[   17.665573] imx415 7-001a: could not get default pinstate
[   17.665575] imx415 7-001a: could not get sleep pinstate
[   17.665577] imx415 7-001a: Looking up dvdd-supply from device tree
[   17.665580] imx415 7-001a: Looking up dvdd-supply property in node /i2c@fec90000/imx415-1@1a failed
[   17.665598] imx415 7-001a: supply dvdd not found, using dummy regulator
[   17.665632] imx415 7-001a: Looking up dovdd-supply from device tree
[   17.665635] imx415 7-001a: Looking up dovdd-supply property in node /i2c@fec90000/imx415-1@1a failed
[   17.665642] imx415 7-001a: supply dovdd not found, using dummy regulator
[   17.665653] imx415 7-001a: Looking up avdd-supply from device tree
[   17.665655] imx415 7-001a: Looking up avdd-supply property in node /i2c@fec90000/imx415-1@1a failed
[   17.665661] imx415 7-001a: supply avdd not found, using dummy regulator
[   17.728448] imx415 7-001a: Detected imx415 id 0000e0
[   17.748219] imx415 7-001a: Consider updating driver imx415 to match on endpoints
[   17.748229] rockchip-csi2-dphy csi2-dphy0: dphy0 matches m00_b_imx415 7-001a:bus type 5
[   20.500537] imx415 7-001a: set hdr cfg, set mode to 0
[   20.500544] imx415 7-001a: set fmt: cur_mode: 3864x2192, hdr: 0, bpp: 10

```

## 预览图像
```bash
gst-launch-1.0 v4l2src device=/dev/video11 ! video/x-raw,format=NV12,width=1920,height=1080, framerate=60/1 ! xvimagesink
```
![](pic/gst.png)

## 运行demo
```bash
pip install opencv-python
python3 demo.py
```
![](pic/demo.png)