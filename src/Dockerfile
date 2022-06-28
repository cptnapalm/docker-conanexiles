FROM ubuntu:22.04

LABEL maintainer="James Patrick Conlon"
LABEL version="0.0"
LABEL description="Conan Exiles Server Docker."

ENV TIMEZONE=Americas/Chicago \
    DEBIAN_FRONTEND=noninteractive \
    CONANEXILES_MASTERSERVER=1 \
    CONANEXILES_Game_RconPlugin_RconEnabled=1 \
    CONANEXILES_Game_RconPlugin_RconPassword=Password \
    CONANEXILES_Game_RconPlugin_RconPort=25575 \
    CONANEXILES_Game_RconPlugin_RconMaxKarma=60 \
    CONANEXILES_Engine_Core.Log_LogStreaming=Warning \
    CONANEXILES_Engine_Core.Log_LogModController=Warning \
    CONANEXILES_Engine_Core.Log_LoglevelActorContainer=Warning \
    CONANEXILES_Game_/Script/ConanSandbox.ConanGameMode_PeriodicBackupInterval=900

RUN apt-get update \
    && apt-get install -y crudini python3-pip redis-tools software-properties-common supervisor unzip curl xvfb wget rsync net-tools \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN dpkg --add-architecture i386 \
    && curl https://dl.winehq.org/wine-builds/winehq.key | apt-key add - \
    && apt-add-repository 'https://dl.winehq.org/wine-builds/ubuntu/' \
#    && apt-add-repository ppa:cybermax-dexter/sdl2-backport \
    && apt-get update \
    && apt-get install --install-recommends --assume-yes winehq-devel \
    && pip3 install python-valve \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install winetricks
ADD https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks /usr/bin/winetricks
RUN chmod +x /usr/bin/winetricks

RUN apt-get update \
    && apt-get install -y x11vnc strace cabextract \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Copies from the github repo's src/ directory to image's root directory
# This happens to be where the Dockerfile is.
# The files conanexiles_controller, rconcli, and steamcmd_setup were on the image
# and have been copied to the from_image directory

# Contents of from_image have been copied to the src folder so the below might work again.
COPY . ./

# Guessing this is how it works.
# Wrong: everything must be in this folder
# COPY ../from_image/usr/bin/. ./usr/bin/

RUN ln -snf /usr/share/zoneinfo/$TIMEZONE /etc/localtime \
    && echo $TIMEZONE > /etc/timezone \
    && chmod +x /entrypoint.sh \
    && cd /usr/bin/ \
    && chmod +x conanexiles_controller rconcli steamcmd_setup

EXPOSE 7777/udp 25575 27015/udp 27016/udp 37015/udp 37016/udp 5900

VOLUME ["/conanexiles"]

ENTRYPOINT ["/entrypoint.sh"]
CMD ["supervisord"]
