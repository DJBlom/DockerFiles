# Image that will be used
FROM fedora:36

# Shell that will be used
SHELL ["/bin/bash", "-c"]


ENV USER observer
ENV HOME_DIR /home/${USER}

# Setting up the user environment
RUN groupadd --gid=1002 ${USER} && \
    useradd --uid=1001 --gid=${USER} --create-home ${USER} && \
    echo "${USER} ALL=(ALL) NOPASSWD : ALL" >> /etc/sudoers && \
    sudo -u ${USER} mkdir /home/${USER}/project && \
    sudo -u ${USER} mkdir /home/${USER}/bin && \
    sudo -u ${USER} mkdir -p /home/${USER}/.local/bin

# Installing all development dependencies
RUN dnf -y install \
    python3 lcov cloc cppcheck git texinfo help2man \
    make cmake dh-autoreconf autoconf automake \
    binutils bison valgrind check gcc gcc-c++ \
    glibc-devel curl wget opencv opencv-devel

# Setup CppUTest
WORKDIR $HOME_DIR
RUN git clone https://github.com/cpputest/cpputest.git

WORKDIR $HOME_DIR/cpputest
RUN autoreconf --install
RUN ./configure
RUN make tdd
ENV CPPUTEST_HOME=$HOME_DIR/cpputest

# Copy The Cross Compiling Toolchain
WORKDIR $HOME_DIR
COPY ./x-tools .
ENV CROSS_COMPILER=$HOME_DIR/aarch64-rpi3-linux-gnu/bin

# Setup OpenCV
WORKDIR $HOME_DIR
RUN git clone --depth 1 --branch 4.8.1 https://github.com/opencv/opencv.git
WORKDIR $HOME_DIR/opencv
RUN mkdir build
RUN cmake -S . -B build
WORKDIR $HOME_DIR/opencv/build
RUN cmake --build .
RUN make install


# Providing system information like adding things to the path and setting up the language to be used
ENV PATH=/home/${USER}/bin:$PATH \
    PATH=/usr/include:$PATH \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US:en \
    LC_ALL=C.UTF-8


# Finnishing off by setting the user and changing into the project directory
WORKDIR $HOME_DIR/project
RUN sudo chown -R ${USER}:${USER} /home/${USER}/
USER ${USER}

