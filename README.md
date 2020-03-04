
# FaceX Docker SDK 

## 📑 Index
* [Getting Started](#getting-started)
   * [Prerequisites](#prerequisites)
   * [Installing](#installing)   
          * [Ubuntu](#ubuntu)
            * [Arch Linux](#arch-linux)    
* [Getting The SDK](#getting-the-sdk)
   * [server_api](#server_api)
   * [data-wrangler](#data-wrangler)
* [Customization](#customization)
   * [Fine Tune the docker-compose.yml](#fine-tune-the-docker-composeyml)
          * [Optional-Setup NVIDIA & CUDA](#optional-setup-nvidia---cuda)
                * [Ubuntu](#ubuntu-1)
* [Website](#website)


## Getting Started
These instructions will help you setup FaceX docker instance on your linux system.

### Prerequisites
Docker CE
Docker-Compose

```
Tested Distributions:-
Ubuntu 
Debian
Arch Linux

Docker version:-
Docker CE 19+
docker-compose 1.2+
```

### Installing 
#### Ubuntu 
Follow these steps to get Docker installed on your system

```
sudo apt remove docker docker-engine docker.io
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo apt-key fingerprint 0EBFCD88
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce
sudo usermod -aG docker $USER
newgrp docker
```
Note if any issue with fingerprint or key, add the missing key to  keyserver

Now install docker-compose

```
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

sudo chmod +x /usr/local/bin/docker-compose

docker-compose --version
```

### Arch Linux

```
sudo pacman -S docker
sudo usermod -aG docker $USER
newgrp docker
sudo systemctl start docker.service
```

This will setup a running docker service .

Now install docker-compose

```
sudo pacman -S docker-compose

docker-compose --version
```

## Getting The SDK

##### Download docker images
Download the docker images from the link provided.Three docker images are available, each with a specific use case.

* server_api.tar
 to get back image vectors and for imagematching.

##### Load the docker image
 Based on requirement load one or multiple docker images to your system after downloading the image, using following command
 
 ```
docker image load -i

eg:-
docker image load -i server_api.tar
```
##### Clone this github repositoy

```
git clone https://github.com/teamfacex/docker_api.git
```

change directory to api.

```
cd docker-sdk/api
```
##### License
If you are a new customer looking for credentials, please email us at team@facex.io
Edit USER environment variable in [docker-compose.yml](). 

```
environment:
 -USER=xxxx  => 
environment:
 -USER=your_user_id
```
Then execute bash script runDocker

```
bash runDocker.sh
```
This will spinup the required docker instance.

* server_api


###### server_api

This instance exposes following apis

* get_face_vec - http://0.0.0.0:5000/get_face_vec

* face_match - http://0.0.0.0:5000/face_match


* get_face_vec

```
request type: POST
url: http://0.0.0.0:5000/get_face_vec || http://0.0.0.0:5000/get_face_vec?det=1

body-form-fields:-

img:face_image_file

use: to get 128dim array of numbers for face comparison

note:
    - param det=1 will find and crop the first detected face in the input image
```

* face_match

```
request type: POST
url: http://0.0.0.0:5000/face_match || http://0.0.0.0:5000/face_match?det=1

body-form-fields:-

img_1:face_image_file_1
img_2:face_image_file_2

use: to compare vector distance between two images and to see if they match or not.

note:
    - param det=1 will find and crop the first detected face in the input image
```


## Customization
### Fine Tune the [docker-compose.yml]()

Params

1)  device & URL


```
#default setting
devices:
 -/dev/video0:/dev/video0
environment:
 -URL=0

#eg change:
devices:
 -/dev/video1:/dev/video0
environment:
 -URL=0

#or:
devices:
 -/dev/video1:/dev/video0
environment:
 -URL=0
```

2)  USER

```
#default setting:
environment:
 -USER=your_user_id
change this to user id given in the email
```


#### Optional-Setup NVIDIA  & CUDA  
##### Ubuntu
Making GPUs accessible in Docker Instance, for GPU accelerated docker instances.

```
# Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker

###########################TESTING###########################
#Optional
#Testing GPUs are accessible
docker run --gpus all nvidia/cuda:9.0-base nvidia-smi
#Testing if CUDA is working
nvidia-docker run  --rm nvidia/cuda nvcc  -V

#############################################################
```

to the startup script add following arguments in runDocker

```
--gpus all
```

to get CUDA mounted, add following to docker run in runDocker

```
-v /usr/local/cuda:/usr/local/cuda
```

## Website 

* **FaceX**  - [facex](https://facex.io)