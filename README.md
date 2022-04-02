# Build CoreELEC in a docker container

## Pull the image and run the container

* `docker pull coreelec/coreelec-builder:latest`
* `docker run -v ~/:/home/docker -h coreelec -it coreelec/coreelec-builder:latest`

## Clone repo and build image inside container

* `sudo apt-get update`
* `git clone git://github.com/CoreELEC/CoreELEC.git ~/CoreELEC`
* `cd ~/CoreELEC`
* `time(make image)`  
Use `PROJECT=Amlogic` to build images for older S912 and S905/X/D devices
