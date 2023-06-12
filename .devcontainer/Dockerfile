FROM isaac-sim:2022.2.1-ubuntu20.04

# Install required packages
RUN export DEBIAN_FRONTEND=noninteractive \
&& apt-get update && apt-get install -y --no-install-recommends \
    git \
    python3-pip \
    python3-setuptools \
    python3-wheel \
    sudo cmake build-essential\
    zsh \
&& rm -rf /var/lib/apt/lists/*

# Add a new user named isaac
RUN useradd --create-home --shell /bin/zsh isaac \
    && echo 'isaac:isaac' | chpasswd \
    && usermod -aG sudo isaac

# Configure sudo to not prompt for a password
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

ENV ISAACSIM_PATH /isaac-sim

# ENV OMNI_SERVER omniverse://localhost/NVIDIA/Assets/Isaac/2022.1
ENV OMNI_USER isaac
ENV OMNI_PASS isaac

RUN chmod -R a+rw /isaac-sim

# install packages
RUN apt-get update && apt-get install -q -y --no-install-recommends \
    bash-completion \
    dirmngr \
    gnupg2 \
    lsb-release \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# setup sources.list
RUN echo "deb http://packages.ros.org/ros2/ubuntu focal main" > /etc/apt/sources.list.d/ros2-latest.list

# setup keys
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654

# setup environment
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8

# install bootstrap tools
RUN apt-get update && apt-get install --no-install-recommends -y \
    build-essential \
    git \
    python3-colcon-common-extensions \
    python3-colcon-mixin \
    python3-rosdep \
    python3-vcstool \
    && rm -rf /var/lib/apt/lists/*

# install python packages
RUN pip3 install -U \
    argcomplete \
    flake8 \
    flake8-blind-except \
    flake8-builtins \
    flake8-class-newline \
    flake8-comprehensions \
    flake8-deprecated \
    flake8-docstrings \
    flake8-import-order \
    flake8-quotes \
    pytest-repeat \
    pytest-rerunfailures
# This is a workaround for pytest not found causing builds to fail
# Following RUN statements tests for regression of https://github.com/ros2/ros2/issues/722
RUN pip3 freeze | grep pytest \
    && python3 -m pytest --version

# bootstrap rosdep
RUN rosdep init \
    && rosdep update

# setup colcon mixin and metadata
RUN colcon mixin add default \
      https://raw.githubusercontent.com/colcon/colcon-mixin-repository/master/index.yaml && \
    colcon mixin update && \
    colcon metadata add default \
      https://raw.githubusercontent.com/colcon/colcon-metadata-repository/master/index.yaml && \
    colcon metadata update

# clone source
ENV ROS2_WS /opt/ros2_ws
RUN mkdir -p $ROS2_WS/src
WORKDIR $ROS2_WS

# build source
RUN colcon \
    build \
    --cmake-args \
      -DSECURITY=ON --no-warn-unused-cli \
    --symlink-install

# setup bashrc
RUN cp /etc/skel/.bashrc ~/

# setup entrypoint
COPY ./ros_entrypoint.sh /

# ENTRYPOINT ["/ros_entrypoint.sh"]
CMD ["bash"]
