# ------------------------------------------------------------------------
#
# Copyright 2018 WSO2, Inc. (http://wso2.com)
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License
#
# ------------------------------------------------------------------------

# set to latest Ubuntu LTS
FROM ubuntu:16.04
MAINTAINER WSO2 Docker Maintainers "dev@wso2.org"

# set user configurations
ARG USER=wso2carbon
ARG USER_ID=802
ARG USER_GROUP=wso2
ARG USER_GROUP_ID=802
ARG USER_HOME=/home/${USER}

ARG DOWNLOAD_URL=http://domain.com/folder
ARG FILES=/home/${USER}/files

# set wso2 product configurations
ARG WSO2_SERVER=wso2ei
ARG WSO2_SERVER_VERSION=6.2.0
ARG WSO2_SERVER_DIST=${WSO2_SERVER}-${WSO2_SERVER_VERSION}
ARG WSO2_SERVER_HOME=${USER_HOME}/${WSO2_SERVER_DIST}
# set jdk configurations
ARG JDK_DIST=jdk1.8.0*
ARG JAVA_HOME=${USER_HOME}/java

# install required packages
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    curl \
    unzip \
    netcat && \
    rm -rf /var/lib/apt/lists/* && \
    echo '[ ! -z "$TERM" -a -r /etc/motd ] && cat /etc/motd' \
    >> /etc/bash.bashrc \
    ; echo "\
    Welcome to WSO2 Docker resources.\n\
    The Docker container contains the WSO2 product with its latest updates, which are under the End User License Agreement (EULA) 2.0.\n\
    \n\
    Read more about EULA 2.0 (https://wso2.com/licenses/wso2-update/2.0).\n"\
    > /etc/motd

# create a user group and a user
RUN groupadd --system -g ${USER_GROUP_ID} ${USER_GROUP} && \
    useradd --system --create-home --home-dir ${USER_HOME} --no-log-init -g ${USER_GROUP_ID} -u ${USER_ID} ${USER} && \
    mkdir ${FILES}

ADD --chown=wso2carbon:wso2 ${DOWNLOAD_URL}/wso2ei-6.2.0.zip ${FILES}
ADD --chown=wso2carbon:wso2 ${DOWNLOAD_URL}/jdk-8u171-linux-x64.tar.gz ${FILES}
ADD --chown=wso2carbon:wso2 ${DOWNLOAD_URL}/mysql-connector-java-5.1.46-bin.jar ${FILES}
ADD --chown=wso2carbon:wso2 ${DOWNLOAD_URL}/mssql-jdbc-6.4.0.jre8.jar ${FILES}

RUN tar -zxvf ${FILES}/jdk-8u171-linux-x64.tar.gz -C ${FILES}
RUN mv ${FILES}/jdk1.8.0_171 ${JAVA_HOME}
RUN unzip ${FILES}/wso2ei-6.2.0.zip -d ${USER_HOME}
RUN mv ${FILES}/mysql-connector-java-5.1.46-bin.jar ${WSO2_SERVER_HOME}/lib
RUN mv ${FILES}/mssql-jdbc-6.4.0.jre8.jar ${WSO2_SERVER_HOME}/lib

RUN chown -R wso2carbon:wso2 ${JAVA_HOME}
RUN chown -R wso2carbon:wso2 ${WSO2_SERVER_HOME}

RUN rm -vR ${FILES}

# copy jdk, wso2 product distribution to user's home directory and copy mysql connector jar to product distribution
#COPY --chown=wso2carbon:wso2 ${FILES}/${JDK_DIST} ${JAVA_HOME}
#COPY --chown=wso2carbon:wso2 ${FILES}/${WSO2_SERVER_DIST} ${WSO2_SERVER_HOME}
#COPY --chown=wso2carbon:wso2 ${FILES}/mysql-connector-java-*-bin.jar ${WSO2_SERVER_HOME}/lib
# artifacts for Kubernetes membership scheme based clustering
#COPY --chown=wso2carbon:wso2 ${FILES}/dnsjava-*.jar ${WSO2_SERVER_HOME}/lib
#COPY --chown=wso2carbon:wso2 ${FILES}/kubernetes-membership-scheme-*.jar ${WSO2_SERVER_HOME}/dropins

# set the user and work directory
USER ${USER}
WORKDIR ${USER_HOME}

# set environment variables
ENV JAVA_HOME=${JAVA_HOME} \
    PATH=$JAVA_HOME/bin:$PATH \
    WSO2_SERVER_HOME=${WSO2_SERVER_HOME} \
    WORKING_DIRECTORY=${USER_HOME}
