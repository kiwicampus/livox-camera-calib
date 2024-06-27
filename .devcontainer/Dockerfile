ARG ROS_VERSION="kinetic"

FROM osrf/ros:${ROS_VERSION}-desktop-full

# Add vscode user with same UID and GID as your host system
# (copied from https://code.visualstudio.com/remote/advancedcontainers/add-nonroot-user#_creating-a-nonroot-user)
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# For wifi configuration and settings
ENV DBUS_SYSTEM_BUS_ADDRESS=unix:path=/host/run/dbus/system_bus_socket

WORKDIR /workspace/livox/

# -----------------------------------------------------------------------------
# Define user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    # ------------------------------
    # [Optional] Add sudo support for non-root user
    && apt-get update && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    # ------------------------------
    # Clean up
    && apt-get autoremove && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/* \
    # ------------------------------
    # WebCam Permission
    && usermod -a -G video $USERNAME \
    # ------------------------------
    # Lidar Permission
    && adduser ${USERNAME} dialout

# Install 
RUN sudo apt update && sudo apt upgrade -y \
    && sudo apt install  --no-install-recommends -y \
    bash-completion \
    git \
    libeigen3-dev \
    libceres-dev \
    && rm -rf /home/ada/.cache/pip

# Rosdep update
RUN rosdep update \
    && sudo rm /etc/machine-id \
    && sudo systemd-machine-id-setup


# Source the ROS setup file
RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /home/${USERNAME}/.bashrc \
    && echo "source /workspace/ros/devel/setup.bash || true" >> /home/${USERNAME}/.bashrc

# -----------------------------------------------------------------------------
# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=
CMD ["/bin/bash"]