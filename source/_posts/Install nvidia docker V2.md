---
title: Install nvidia docker 
tags: [DL]
date: 2019-05-04
---

For DL research, it is recommanded to use docker to manage Cuda and Cudnn version, since the the combination of Cuda and Cudann is very strict, if you install the wrong Cuda and Cudnn combination, you will get tons of error when running tensorflow or pytorch.

[nvidia gpu cloud](<https://ngc.nvidia.com/catalog/landing>) is a docker platform maintained by Nvidia, in this platform, we can get doker for tensorflow or pytorch with specific Cuda and Cudnn installed, which help engineers or researchers to make DL developping more efficient.

To use run Nvidia docker, we need to install docker and Nvidia docker v2 first, in this article, I will note down  my practice.

**PS: Nvidia docker V2 is a docker software, Nvidia dockers in [nvidia gpu cloud](<https://ngc.nvidia.com/catalog/landing>) are docker images, they are different, in this article, I will talk about installation of the software**



## Install nvidia driver

Before installing Nvidia docker V2, we need to install graphic driver to make the graphic like: GTX 1080Ti available 

- Command for install:

```shell
# Install driver
sudo apt-get purge nvidia-*
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt-get update
sudo apt-get install nvidia-418

# Test with the command
nvidia-smi

# If nvidia-smi not work, try to reboot
sudo reboot

```



## Install docker ce 18.09.1

Need to install docker ce berfore nvidia docker v2

- Command for install:

```shell
# Uninstall old versions
sudo apt-get remove docker docker-engine docker.io containerd runc

# Update the apt package index
sudo apt-get update

# Install packages to allow apt to use a repository over HTTPS
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common
# Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Verify that you now have the key with the fingerprint 9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88, by searching for the last 8 characters of the fingerprint.
sudo apt-key fingerprint 0EBFCD88

# Set up the stable repository
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
sudo apt-get update

# Install docker ce  with lastest version
sudo apt-get install docker-ce docker-ce-cli containerd.io

# List the version of all the docekr ce
apt-cache madison docker-ce

# Install a specific version using the version string from the second column, for example, 5:18.09.1~3-0~ubuntu-xenial.
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

# Verify the result
docker -v
sudo docker run hello-world


```



- Check if was installed successfully

```shell
# Check docker version
docker -v
```



- Tips:

1. when running docker , system may get  permission error, you can use 'sudo' to get rid of it , if you want to solve the problem with a better way ,you can fix the docker permission:

   ```shell
   # Add the current user to the docker group
   sudo usermod -a -G docker $USER
   
   # After setting, remember to log out then log in
   exit
   ```

 



## Install nvidia docker v2

Nvida docker v2 can make the Cuda for Nvidia docker available.

- Command for install:

```shell
# If you have nvidia-docker 1.0 installed: we need to remove it and all existing GPU containers
docker volume ls -q -f driver=nvidia-docker | xargs -r -I{} -n1 docker ps -q -a -f volume={} | xargs -r docker rm -f
sudo apt-get purge -y nvidia-docker

# Add the package repositories
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update

# Install nvidia-docker2 and reload the Docker daemon configuration
sudo apt-get install -y nvidia-docker2
sudo pkill -SIGHUP dockerd

# Test nvidia-smi with the latest official CUDA image
docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
```



- Check if was installed successfully

```shell
# Test nvidia-smi with the latest official CUDA image
docker run --runtime=nvidia --rm nvidia/cuda:9.0-base nvidia-smi
```



- Tips:

Sometime, when testing the nvida docker 2 with nvidia-smi function,  may come across error, try to uninstall nvidia driver , reinstall nvidia driver and reboot



# Reference

[Nvidia docker github repo](<https://github.com/NVIDIA/nvidia-docker>)

[Get Docker CE for Ubuntu](<https://docs.docker.com/install/linux/docker-ce/ubuntu/>)

[Nividia docker V2 install](<https://gist.github.com/Brainiarc7/a8ab5f89494d053003454efc3be2d2ef>)



