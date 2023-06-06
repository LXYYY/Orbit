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

# Set the user to isaac

WORKDIR /workspace/Orbit

ENV ISAACSIM_PATH /isaac-sim

# ENV OMNI_SERVER omniverse://localhost/NVIDIA/Assets/Isaac/2022.1
ENV OMNI_USER isaac
ENV OMNI_PASS isaac

RUN chown -R ${OMNI_USER}:${OMNI_USER} /isaac-sim