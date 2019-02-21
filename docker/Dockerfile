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