# Use ROS 2 Jazzy for Ubuntu 24.04
FROM osrf/ros:jazzy-desktop-full

# Set non-interactive mode for apt-get
ENV DEBIAN_FRONTEND=noninteractive

# Install development tools and robotics hardware support
RUN apt-get update && apt-get install -y \
    # Python development
    python3-pip \
    python3-venv \
    # C++ toolchain
    g++ \
    gcc \
    cmake \
    make \
    gdb \
    # ROS development tools
    python3-colcon-common-extensions \
    python3-rosdep \
    # Hardware tools (gamepad/serial)
    evtest \
    jstest-gtk \
    libcanberra-gtk-module \
    libcanberra-gtk3-module \
    python3-serial \
    # Utilities
    git \
    vim \
    nano \
    tree \
    curl \
    wget \
    man-db \
    bash-completion \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user
ARG USERNAME=developer
ARG USER_UID=1000
ARG USER_GID=1000

# Delete existing user with UID 1000 if exists (usually 'ubuntu')
RUN if getent passwd $USER_UID > /dev/null; then \
        userdel -r $(getent passwd $USER_UID | cut -d: -f1); \
    fi && \
    if getent group $USER_GID > /dev/null; then \
        groupdel $(getent group $USER_GID | cut -d: -f1); \
    fi && \
    groupadd --gid $USER_GID $USERNAME && \
    useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME && \
    apt-get update && \
    apt-get install -y sudo && \
    echo "$USERNAME ALL=(root) NOPASSWD: ALL" > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME && \
    rm -rf /var/lib/apt/lists/*

# Add user to dialout group for serial/gamepad access
RUN usermod -aG dialout ${USERNAME}

# Initialize rosdep
RUN rosdep init || true
USER $USERNAME
RUN rosdep update

# Set working directory
WORKDIR /home/$USERNAME/workspace

# Copy configuration files
USER root
COPY bashrc /home/$USERNAME/.bashrc
RUN chown $USERNAME:$USERNAME /home/$USERNAME/.bashrc

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

USER $USERNAME

ENTRYPOINT ["/entrypoint.sh"]
CMD ["bash"]