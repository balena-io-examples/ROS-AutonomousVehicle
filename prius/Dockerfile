FROM osrf/ros:kinetic-desktop
ENV UDEV=1
ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update \
 && apt-get install -y \
    wget \
    nano \
    lsb-release \
    sudo \
    mesa-utils \
    xinit \
    x11-xserver-utils \
    xinput \
    dbus-x11 \
    xserver-xorg-input-evdev \
    xterm \
    udev \
    xorg xserver-xorg xserver-xorg-core \ 
    openbox \
    xserver-xorg-legacy \
    x11-xserver-utils \ 
    libgtk-3-dev \
    libnss3-dev \
    libxss1 \
    libasound2-dev \
    openbox \ 
 && apt-get clean

# Get gazebo binaries
RUN echo "deb http://packages.osrfoundation.org/gazebo/ubuntu `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list \
 && wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add - \
 && apt-get update \
 && apt-get install -y \
    gazebo9 \
    ros-kinetic-gazebo9-ros-pkgs \
    ros-kinetic-fake-localization \
    ros-kinetic-joy \
 && apt-get clean

RUN mkdir -p /tmp/workspace/src
COPY prius_description /tmp/workspace/src/prius_description
COPY prius_msgs /tmp/workspace/src/prius_msgs
COPY car_demo /tmp/workspace/src/car_demo
RUN /bin/bash -c 'cd /tmp/workspace \
 && source /opt/ros/kinetic/setup.bash \
 && catkin_make'
COPY start.sh /start.sh
COPY entry.sh /usr/bin/entry.sh
WORKDIR /usr/src/app

# Add prius user
RUN useradd prius -m -s /bin/bash -G root && \
    groupadd -r -f prius && id -u prius

RUN mkdir /home/prius/.config
RUN mkdir /home/prius/.config/openbox
COPY ./autostart /home/prius/.config/openbox/autostart
COPY ./rc.xml /home/prius/.config/openbox/rc.xml
RUN mkdir '/home/prius/.config/ROS Desktop'
RUN chown -R prius:prius /home/prius

COPY ./menu.xml /var/lib/openbox/debian-menu.xml

# udev rule to set specific permissions 
RUN echo 'SUBSYSTEM=="vchiq",GROUP="video",MODE="0660"' > /etc/udev/rules.d/10-vchiq-permissions.rules
RUN usermod -a -G audio,video,tty prius

ENTRYPOINT ["bash", "/start.sh"]
CMD ["export DISPLAY=:0"]
