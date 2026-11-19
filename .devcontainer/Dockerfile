# CaveX devcontainer image

# This container was developed to support the 26/27 Research Project
# and designed to be used as an Ubuntu 20.04 environment on any 
# machine (especially as most of the software is EOL). 
# This image should include the nessacary dependancies i.e ROS Noetic + eigen3 + Ceres ...

##NOTES because i havent made a readme yet
# -container will not detect controller unless it is connected before opening


#brings in ROS, RViz, Gazebo 11
FROM osrf/ros:noetic-desktop-full AS deps
ARG DEBIAN_FRONTEND=noninteractive
################################################################################


################################################################################
# Ceres has to be built from source as Ubuntu 20.04 apt doesnt include 2.1.0
# This part of the image will take ~approx 5-10 mins to build the first time the 
# environment is built
ARG CERES_VERSION=2.1.0
#this could be wrong tbh the readme was literally in Mandarin
RUN <<EOF
    set -eux  
    apt-get update -y  
    apt-get install -y git wget sudo mesa-utils \
    cmake libeigen3-dev libgoogle-glog-dev libgflags-dev \
    libatlas-base-dev libsuitesparse-dev
    cd /tmp
    wget -q -O ceres-solver-${CERES_VERSION}.tar.gz \
    https://github.com/ceres-solver/ceres-solver/archive/refs/tags/${CERES_VERSION}.tar.gz
    tar -xzf ceres-solver-${CERES_VERSION}.tar.gz
    mkdir ceres-solver-${CERES_VERSION}/build
    cd ceres-solver-${CERES_VERSION}/build
    cmake .. -DBUILD_TESTING=OFF -DBUILD_EXAMPLES=OFF
    make -j$(nproc) install
    cd /
    rm -rf /tmp/ceres-solver-${CERES_VERSION}*
    rm -rf /var/lib/apt/lists/* 
EOF

################################################################################
# Core Dependancies (from the openSHC readme and the CaveX readme)
RUN apt-get update -y && apt-get install -y  \
#openSHC dependancies
    ros-noetic-joy \
    ros-noetic-ros-control \
    ros-noetic-ros-controllers \
    python3-catkin-tools \
#CaveX dependancies
    libpcl-dev \
    libeigen3-dev \
#Add extra dependancies here:
#-->
    && rm -rf /var/lib/apt/lists/*



FROM deps AS env
################################################################################
# Environment setup

# Source ros environment for user's shell
RUN echo "source /opt/ros/noetic/setup.bash" >> /etc/bash.bashrc

# non-root user (UID 1000) so created files arent owned by root on the host.
ARG USERNAME=cavex27
# adds video control for user (needed for rviz etc)
RUN useradd -m -s /bin/bash -u 1000 -G sudo,video ${USERNAME} \
 && echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${USERNAME}
USER ${USERNAME}


# Default command is to enter an interactive session
CMD /bin/bash

