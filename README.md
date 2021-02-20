# dockeradmin

# Overview

The goal is to provide an `dockeradmin` script to build out a Docker base image. There are many ways to perform this task. The setup provided here assumes a RHEL-based target environment. Using [moby/moby](https://github.com/moby/moby) as a reference, this project modifies [mkimage-yum.sh](https://github.com/moby/moby/blob/master/contrib/mkimage-yum.sh) to match up with development requirements.

```
dockeradmin [options] imagename|/path/to/imgenv
  imgenv             The full path to the imgenv file
  imagename          Build the image using defaults and the specified imagename (i.e. the base OS)
Options
  -y  <yum.conf>     The full path to the yum.conf - default to /etc/yum.conf
  -t  <tag>          Tag name - default 'latest'
  -s  <imgfile>      Import the image into Docker and perform a 'docker save' of image to imgfile (i.e. /tmp/image.tar.gz).
  -h                 Print the help message
```

# Image Environment

Configuration file detailing the following.

```
# Example

imagename=sample-os

packages=("kernel.x86_64" "yum.noarch" "unzip")

rpms=("/tmp/jdk-11.0.4_linux-x64_bin.rpm")

scripts=("/tmp/install_node.sh" "/tmp/install_angular.sh")
```

1. (mandatory) `imagename` The image name.

2. `packages` Full name of yum packages to install (i.e. kernel.x86_64).

3. `rpms` Full path of RPM files on the local machine to install as part of the base image.

4. `scripts` Full path script files on the local machine to execute as part of the base image. These scripts can be used to stage files and must accept at least 1 argument, with the first being the target location for th Docker image file.

Note: If `packages`, `rpms`, and `scripts` are empty or if `imagename` is used instead, then the image will include the base kernal and mandatory packages for the OS.

# Assumptions

1. The machine running `dockeradmin` is connected to a valid yum repository.

2. The user has `sudo` privileges.

# Building a Base Image

1. As root: `dockeradmin -s /vagrant/docker/base-os.tar.gz base-os`

2. As root: `docker image ls --all` to confirm local installed image (i.e. base-os:latest)

3. Use the docker image in your application `Dockerfile`

## Example: Build a JDK Base Image

1. Download the [JDK 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

2. Create a base image `basejdk.env` file (below)

3. As root: `dockeradmin /path/to/basejdk.env`

4. As root: `docker run --rm basejdk:latest java -version`

```
# basejdk.env
imagename=basejdk
packages=()
rpms=('/tmp/jdk-11.0.4_linux-x64_bin.rpm')
scripts=()
```

```
sudo docker run --rm basejdk:latest java -version
java version "11.0.4" 2019-07-16 LTS
Java(TM) SE Runtime Environment 18.9 (build 11.0.4+10-LTS)
Java HotSpot(TM) 64-Bit Server VM 18.9 (build 11.0.4+10-LTS, mixed mode)
```

# Resources

- https://docs.docker.com/engine/install/binaries/

- https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-centos-7

- https://docs.docker.com/registry/

- https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-18-04c