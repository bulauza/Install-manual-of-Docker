# Install-manual-of-Docker
This is a note on how to install Docker on Ubuntu 
## Introduction
### 対象者
* Ubuntu環境でDockerを使ってみたい人．  
* DockerでGPUを使いたい人（2章へ）．  
  * Windowsだと使えないらしい

### 筆者の環境  
CPU: Intel corei9-9900k  
GPU: NVIDIA GeForce RTX2070  
OS:  ubuntu 18.0.4

### 手順
1. CPUで動く普通のDockerを導入
2. GPUを使えるようにするnvidia-dockerを導入  

## 1. Install Docker CE
[公式](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
の手順通り Docker CE(安定版)を導入  
### Command
```
$ sudo apt-get update
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
$ sudo apt-key fingerprint 0EBFCD88
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io 
```
以下を実行した出力の2列目（例.`5:18.09.1~3-0~ubuntu-xenial`)をメモ．  
```
$ apt-cache madison docker-ce

  docker-ce | 5:18.09.1~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 5:18.09.0~3-0~ubuntu-xenial | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 18.06.1~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  docker-ce | 18.06.0~ce~3-0~ubuntu       | https://download.docker.com/linux/ubuntu  xenial/stable amd64 Packages
  ...
```
<VERSION_STRING>をメモに書き換えて以下を実行．  
`$ sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io`  
ここでCPUマシンの人は終了．  
恒例のハロワで確認．  
`$ sudo docker run hello-world`  
GPUマシンの人は引き続きnvidia-dockerの導入をしていきます．  


## 2. Install nvidia-docker2
DockerでGPUを使うためのnvidia-docker2を導入．  
[公式](https://github.com/NVIDIA/nvidia-docker)はここ．  

### Prerequisites  
* ホストとなるOSに NVIDIA driver を導入していること．  
`$ nvidia-smi`で表が出てくるか確認してください．  
* nvidia-docker を初めて導入する人．  
古いものがあれば消す必要があるのでそういう人は公式ページを参照．  
このページでは紹介しません．  

### Command
```
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
$ curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
$ curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

$ sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
$ sudo systemctl restart docker
```
確認のため`$ docker run --runtime nvidia nvidia/cuda:9.0-base nvidia-smi`を実行．  
見慣れた表が出てきたら成功です．  
お疲れ様でした．  
