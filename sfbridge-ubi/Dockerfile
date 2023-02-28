# © Copyright IBM Corporation 2015, 2018
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
# limitations under the License.

ARG BASE_IMAGE=registry.access.redhat.com/ubi8/ubi-minimal
ARG BASE_TAG=8.7-1049.1675784874

FROM $BASE_IMAGE:$BASE_TAG

LABEL summary="IBM MQ Advanced Server" \
      description="Simplify, accelerate and facilitate the reliable exchange of data with a security-rich messaging solution — trusted by the world’s most successful enterprises" \
      vendor="IBM" \
      maintainer="IBM" \
      distribution-scope="private" \
      authoritative-source-url="https://www.ibm.com/software/passportadvantage/" \
      url="https://www.ibm.com/products/mq/advanced" \
      io.openshift.tags="mq messaging" \
      io.k8s.display-name="IBM MQ Advanced Server" \
      io.k8s.description="Simplify, accelerate and facilitate the reliable exchange of data with a security-rich messaging solution — trusted by the world’s most successful enterprises" \
      base-image=$BASE_IMAGE \
      base-image-release=$BASE_TAG

# The URL to download the MQ installer from in tar.gz format
ARG MQ_URL=http://172.17.0.1:9000/IBM_MQ_9.3.0.2_LINUX_X86-64.tar.gz

# The MQ packages to install
ARG MQ_PACKAGES="MQSeriesRuntime-*.rpm MQSeriesJava-*.rpm MQSeriesJRE-*.rpm MQSeriesSFBridge-*.rpm" 

# Install additional packages required by MQ, this install process and the runtime scripts
RUN microdnf --disableplugin=subscription-manager install bash bc ca-certificates file findutils gawk glibc-common grep ncurses-compat-libs passwd procps-ng sed shadow-utils tar util-linux which rpm-build \
   && microdnf --disableplugin=subscription-manager clean all \
  # Download and extract the MQ installation files
   && export DIR_EXTRACT=/tmp/mq \
   && mkdir -p ${DIR_EXTRACT} \
   && cd ${DIR_EXTRACT} \ 
   && curl -LO $MQ_URL \
   && tar -zxvf ./*.tar.gz \
  # Recommended: Create the mqm user ID with a fixed UID and group, so that the file permissions work between different images
   && groupadd --system --gid 999 mqm \
   && useradd --system --uid 999 --gid mqm mqm \
   && usermod -G mqm root \
  # Find location of mqlicense.sh
   && export MQLICENSE=$(find ${DIR_EXTRACT} -name "mqlicense.sh") \
  # Accept the MQ license
   && ${MQLICENSE} -text_only -accept \
  # Install MQ using the RPM packages
   && cd MQServer \
   && rpm -Uvh $MQ_PACKAGES \
   && cd .. \
  # Recommended: Set the default MQ installation (makes the MQ commands available on the PATH)
   && /opt/mqm/bin/setmqinst -p /opt/mqm -i \
  # Optional: Set these values for the Bluemix Vulnerability Report
   && sed -i 's/PASS_MAX_DAYS\t99999/PASS_MAX_DAYS\t90/' /etc/login.defs \
   && sed -i 's/PASS_MIN_DAYS\t0/PASS_MIN_DAYS\t1/' /etc/login.defs \
   && sed -i 's/# minlen/minlen/' /etc/security/pwquality.conf \
   && sed -i 's/password\t\[success=1 default=ignore\]\tpam_unix\.so obscure sha512/password\t[success=1 default=ignore]\tpam_unix.so obscure sha512 minlen=8/' /etc/pam.d/password-auth

ENV LANG=en_US.UTF-8

#Create directory for MQ Salesforce bridge config files
RUN mkdir /etc/mqm \
   && mkdir -p /var/mqm/qmgrs/TS1 \
   && mkdir -p /var/mqm/qmgrs/TS1/ssl \
   && mkdir -p /var/mqm/qmgrs/TS1/log

#Copy MQ Salesforce bridge configuration and run scripts
COPY mqsf-configure-sfbridge.sh /usr/local/bin/mqsf-config
COPY mqsf-run-sfbridge.sh /usr/local/bin/mqsf-run
COPY AMQCLCHL.TAB /var/mqm/qmgrs/TS1/AMQCLCHL.TAB
COPY keystore.jks /var/mqm/qmgrs/TS1/ssl/keystore.jks

#Make executable
RUN chmod u+x /usr/local/bin/mqsf-config \
   && chmod u+x /usr/local/bin/mqsf-run

#Assign build args for MQ Salesforce bridge configuration
ARG runmqsfb_QueueManager
ARG runmqsfb_MQBaseTopic
ARG runmqsfb_MQChannel
ARG runmqsfb_MQConname
ARG runmqsfb_MQDLQname
ARG runmqsfb_MQCCDTURL
ARG runmqsfb_JndiClass
ARG runmqsfb_JndiProvider
ARG runmqsfb_MQUserName
ARG runmqsfb_MQPassword
ARG runmqsfb_SFUserName
ARG runmqsfb_SFPassword
ARG runmqsfb_SFToken
ARG runmqsfb_SFEndpoint
ARG runmqsfb_SFConsumerKey
ARG runmqsfb_SFConsumerSecret
ARG runmqsfb_KeyStoreFileName
ARG runmqsfb_KeyStorePassword
ARG runmqsfb_TrustedStoreFileName
ARG runmqsfb_TrustedStorePassword:
ARG runmqsfb_UseTLSforMQ
ARG runmqsfb_PushTopics
ARG runmqsfb_PlatformEvents
ARG runmqsfb_MonitorInterval
ARG runmqsfb_QoS
ARG runmqsfb_MQSubscribe
ARG runmqsfb_AppDataFormat
ARG runmqsfb_StartupWait
ARG runmqsfb_RuntimeLogFile

#Configure template file for MQ Salesforce bridge
RUN /usr/local/bin/mqsf-config

#Set entrypoint for MQ Salesforce bridge runtime
ENTRYPOINT ["mqsf-run"]
