---
title: Typora实现云端和图床
date: 2025-06-18
summary: Typora实现云端和图床
image: https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202508301759298.jpg
---

# Typora实现云端和图床

## 所需工具

1. typora
2. github账号
3. oss（此处用阿里云）
4. picgo

## 配置过程

### 阿里云oss

1. 打开[对象存储 OSS](https://common-buy.aliyun.com/?spm=5176.7933691.J_5253785160.2.2e392c479oeCKX&commodityCode=ossbag&request=%7B%22ord_time%22:%221:Year%22,%22order_num%22:1,%22ossbag_type%22:%22storage_std_zrs%22,%22region%22:%22china-common%22,%22pack%22:%22FPT_ossbag_absolute_1572089992%22,%22std_zrs_storage_bag_spec%22:%22500%22,%22showtime%22:%22now%22%7D&regionId=china-common)，选择本地冗余存储+40gb+一年（共计9元每年）

![image-20250614214853690](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220255.png)

2. 打开[OSS管理控制台](https://oss.console.aliyun.com/overview)，点击`Bucket列表`，点击`创建Bucket`，创建名称，选择一个离你近的城市，如“成都”，然后完成创建。(**记住你的Bucket名称和创建城市**)

![image-20250614215514412](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220994.png)

3. 打开你创建的Bucket，选择`权限控制`->`读写 权限`，开启`公共读`。

![image-20250614215756452](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220731.png)

4. 点击右上角头像的`AccessKey`

![image-20250614215907407](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220338.png)

5. 创建AccessKey，记住`KeyId`和`KeySecret`

### PicGo

1. 下载[PicGo is Here | PicGo](https://picgo.github.io/PicGo-Doc/zh/guide/#下载安装)，并记住你的安装路径

![image-20250614220240909](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220733.png)

2. 打开PicGo，选择`图床设置`->`阿里云OSS`，在`设置KeyId`和`设定KeySecret`处填写刚刚创建的`KeyId`和`KeySecret`，在`设定Bucket`和`设定存储区域`处填写Bucket的名称和`oss-cn-`加上你所创的的区域，区域为中文配音，如`oss-cn-chengdu`，`设定存储路径`为自定义，可以去阿里云处新建文件夹

![image-20250614220407505](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220101.png)

3. 点击`PicGo设置`中开启`时间戳重命名`和`上传后自动复制URL`

### typora

1. `偏好设置`->`图像`->`上传服务`，选择`PicGo（app）`，打开刚才下载PicGo的路径，选择exe文件，验证图片是否上传成功。

![image-20250614221122751](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220892.png)

### github实现同步

1. 新建仓库，任意命名，选择私密仓库(`Private`)

![image-20250614221242362](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220173.png)

2. 回到本地，创建一个文件夹，在git bash中输入`git init`
3. 在git中输入`New-Item .gitignore`，使用vscode（或者记事本）打开``.gitignore`，在其中输入过滤掉不用上传的文件，如`*.jpg`，`*.png`等
4. 回到github仓库，执行下面的命令，即可实现云端上传

```git
git remote add origin https://github.com/UserName/XXXX.git
git branch -M main
git push -u origin main
```

![image-20250614221737528](https://picgo-elaina.oss-cn-chengdu.aliyuncs.com/typora/202506142220827.png)

5. 回到刚刚创建的本地目录，边写markdown文件，写完后执行下面的命令，进行文件上传

```git
git add .
git commit -m "some changed"
git push
```

