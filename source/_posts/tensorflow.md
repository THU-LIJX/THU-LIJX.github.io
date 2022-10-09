---
title: tensorflow
date: 2022-08-03 19:58:19
updated: 2022-08-03 19:58:19
tags:
---

## TensorFlow指北

### 数据加载

使用tensorflow_datasets加载数据的话需要挂vpn

```shell
export HTTPS_PROXY=http://127.0.0.1:7890
```

下载好的数据集会放在`~/tensorflow_datasets/downloads`下面，通常会有一个`tar.gz`和一个`INFO`文件，解压之后的文件会放在`extracted`里面，里面会有一个将`tar.gz`解压后的文件夹，会有一个`TAR_GZ.`的前缀。

```shell
# tar.gz文件
cs.toronto.edu_kriz_cifar-10-binaryxKOMUKG8XzocVTfyFVq51o-fJese2Nnd2j2ymlm8od0.tar.gz
# 解压后的文件
TAR_GZ.cs.toronto.edu_kriz_cifar-10-binaryxKOMUKG8XzocVTfyFVq51o-fJese2Nnd2j2ymlm8od0.tar.gz
```

