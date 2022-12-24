# Image name: genmon_ubuntu:latest
# https://github.com/JMVS/genmon-docker
# rev 01
# 2022/11/15

# Base image
FROM ubuntu:latest

# Update & install dependencies
RUN apt-get -y update && apt-get -y upgrade
RUN DEBIAN_FRONTEND="noninteractive" TZ="America/Argentina/Buenos_Aires" apt-get install -yq --no-install-recommends sudo cron curl nano git build-essential libssl-dev libffi-dev python3-dev tzdata python3-pip

# Upgrade PIP
RUN sudo pip3 install --upgrade pip

# Clone genmon's repository & set working directory
RUN git clone https://github.com/jgyates/genmon.git
WORKDIR /genmon

# Tells genmon to use Serial over TCP & set a dummuy IP (if these paramenters are not set, genmon will refuse to start)
RUN sed -i "s|use_serial_tcp = False|use_serial_tcp = True|g" ./conf/genmon.conf && sed -i "s|serial_tcp_address =|serial_tcp_address = 192.168.1.1|g" ./conf/genmon.conf 

# Set Serial port to 6638 as per Genmon-ESP32-Serial-Bridge default option (not really required, convinience only)
RUN sed -i "s|serial_tcp_port = 8899|serial_tcp_port = 6638|g" ./conf/genmon.conf

# Let genmon's install script handle the rest (for some reason the default config files are not copied. They will be copied on first start of container)
RUN sudo chmod 775 ./genmonmaint.sh && bash ./genmonmaint.sh -i -n -p 3 -s

# Install another dependency not handled by install script but that genmon complaints on first run
RUN pip install spidev

# Cleanup
RUN apt-get autoremove -y && apt-get clean

# Expose these directories to host
VOLUME /etc/genmon
VOLUME /var/log

# Expose these ports
EXPOSE 443
EXPOSE 8000

# Run this command on container start
CMD bash -c "/genmon/startgenmon.sh start && tail -f /var/log/genmon.log"
