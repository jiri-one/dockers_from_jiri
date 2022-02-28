# This is docker image with Seafile-server using SQLite for AMD64 and ARM64 (RPi4 in my case)
**Dockerfile is multiarch version. Source of Seafile-server is official binary file from [https://www.seafile.com/en/download/](https://www.seafile.com/en/download/), so you will not compile whole code and the creating of docker image is realy fast.**

## How to install this docker image:

**1. Build it yourself:**

Docker command to build:

`docker build -t seafile_sqlite:9.0.2 DIR_WITH_Dockerfile`

Run it with this command and it will build image for you architecture:

`docker run -d --restart unless-stopped --name seafile_sqlite -e SEAFILE_ADMIN=CHANGE@ME.org -e SEAFILE_ADMIN_PW=changemetoo -e SEAHUB_PORT=9000 -e SEAFILE_PORT=9082 -p 9000:9000 -p 9082:9082 -v /your/data/dir:/data seafile_sqlite:9.0.2`

(You can choose any port which you want and don't forget your own admin login and password)

**2. Or you can use [crossbuilding](https://docs.docker.com/buildx/working-with-buildx/) (which is little bit more complicated):**

You need to install qemu-user-static (for example from [ArchLinux AUR](https://aur.archlinux.org/packages/qemu-user-static-bin), like in following example)

`pikaur -S qemu-user-static-bin`

Then you need run this command:

`docker run --rm --privileged multiarch/qemu-user-static --reset -p yes`

(OPTIONAL) Then you can run check, if you realy can build for more architectures:

`docker buildx ls`

More info about cross-compiling is here:
https://wiki.archlinux.org/title/Docker#Using_buildx_for_cross-compiling
and
https://docs.docker.com/buildx/working-with-buildx/

Now you can choose from two versions:
AMD64 - `docker build --platform=linux/amd64 DIR_WITH_Dockerfile`
ARM64 - `docker build --platform=linux/arm64 DIR_WITH_Dockerfile`

This will create unnamed and untaged image, so find the last created image:

`docker images`

Copy the image ID (in my example it is docker 4f3fe188383b) and then give the image proper name:

`docker image tag 4f3fe188383b seafile_sqlite:9.0.2`

(or any name, which you think that it is good for you)


