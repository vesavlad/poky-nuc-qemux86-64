# YOCTO (thud)

> The **Yocto Project(r)** is a [Linux Foundation](https://en.wikipedia.org/wiki/Linux_Foundation) collaborative [open source](https://en.wikipedia.org/wiki/Open-source_software) project whose goal is to produce tools and processes that enable the creation of [Linux distributions](https://en.wikipedia.org/wiki/Linux_distribution) for [embedded and IoT software](https://en.wikipedia.org/wiki/Embedded_software) that are independent of the underlying architecture of the embedded hardware. The project was announced by the Linux Foundation in 2010 and launched in March, 2011, in collaboration with 22 organizations, including [OpenEmbedded](https://en.wikipedia.org/wiki/OpenEmbedded).[[1\]](https://en.wikipedia.org/wiki/Yocto_Project#cite_note-1)
>
> The Yocto Project's focus is on improving the software development process for [embedded Linux](https://en.wikipedia.org/wiki/Embedded_Linux) distributions. The Yocto Project provides interoperable tools, metadata, and processes that enable the rapid, repeatable development of Linux-based [embedded systems](https://en.wikipedia.org/wiki/Embedded_systems) in which every aspect of the development process can be customized.
>
> In October 2018, [Arm Holdings](https://en.wikipedia.org/wiki/Arm_Holdings) partnered with [Intel](https://en.wikipedia.org/wiki/Intel) in order to share code for [embedded systems](https://en.wikipedia.org/wiki/Embedded_systems) through the Yocto Project.[[2\]](https://en.wikipedia.org/wiki/Yocto_Project#cite_note-2)

## 1. Included layers

- meta-ivi
- meta-openembedded (https://layers.openembedded.org/layerindex)

## 2. Initialization of the build

To do the initialization navigate to poky folder and run the **oe-init-build-env** command:

```bash
cd poky
source ./oe-init-build-env ../build
```

The build directory should be one level higher specified with **../build**

## 3. Build

To configure the kernel run ```bitbake linux-yocto -c menuconfig``` after this a shell should popup giving you possibilities to fine tune the kernel. 

Currently the version **4.14** is set as active, but there is support also for version **4.18** if you want new kernel with new features. To activate it simple comment the following lines from the **local.conf** file.

```yaml
PREFERRED_PROVIDER_virtual/kernel ?= "linux-yocto"
PREFERRED_VERSION_linux-yocto ?= "4.14%"
```



To do the build you need to:

- create the image that runs on the board or inside the emulator
- create the SDK that offers you possibilities to cross-compile your code for the board



Here are the commands for building for weston shell support.

```bash
# populate the sdk
bitbake core-image-weston -c populate_sdk

# generate image
bitbake core-image-weston
```



## 4. Docker machine for building this

If you are comfortable with docker you can chose to run the build using a docker container and therefore not have your system bloated with the required tools to get the build running.

The following **Dockerfile** has everything you need to get the build up and running.

```dockerfile
FROM ubuntu:18.04
MAINTAINER Vlad Vesa<hello@vladvesa.ro>

# INSTALL REQUIRED Yocto toolchain
RUN apt-get update && apt-get install -y gawk wget git-core diffstat unzip texinfo gcc-multilib \
    build-essential chrpath socat cpio python python3 python3-pip python3-pexpect \
    xz-utils debianutils iputils-ping libsdl1.2-dev xterm

# INSTALL GIT for cloning POKY
RUN apt-get install -y git locales screen

# Set the locale
RUN sed -i -e 's/# en_US.UTF-8 UTF-8/en_US.UTF-8 UTF-8/' /etc/locale.gen && locale-gen
ENV LANG en_US.UTF-8  
ENV LANGUAGE en_US:en  
ENV LC_ALL en_US.UTF-8   

# add build script
ADD init.sh /bin/init-yocto

RUN groupadd -g 999 yocto && \
    useradd -r -u 999 -g yocto yocto
USER yocto

VOLUME [ "/build" ]

WORKDIR /build

CMD [ "/bin/bash" ]
```

To run a build using docker you need to have the docker installed on your computer (please check docker site in order to clarify the required steps).

I usually end up using the **docker-compose** allot because it gives me flexibility and short commands to remember.

To start the container using **docker-compose** you need to do the followings:

```bash
# make sure you are in the root directory of this repository
docker-compose build
docker-compose run yocto /bin/bash
# from now on you can follow the commands presented earlier
```

