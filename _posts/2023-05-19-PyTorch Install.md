---
title: "PyTorch Install"
categories:
  - Tec
tags:
  - Python
  - AI
toc: true
---
Install Pytorch，And some AI tools

# Install Pytorch

## Install Anaconda

下载
> <https://www.anaconda.com/download/>

教程
> <https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#>

```sh
# check envs
conda info --envs
conda create --name pytorch
conda activate pytorch
```

```sh
# check cuda version in powershell
nvidia-smi
```

## Install PyTorch

https://pytorch.org/get-started/locally/#windows-installation

```sh
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
```

## Install MNIST

```sh
conda install torchvision 
```

## Install matplotlib
该工具可以通过tensor中存储的二维数据绘制图片

```sh
conda install matplotlib 
```