FROM       centos:centos7
MAINTAINER Jason Swank <docker@scalene.net>

ENV NEXUS_DATA /nexus-data

ENV NEXUS_VERSION 3.0.0-b2015110601

ENV JAVA_HOME /opt/java
ENV JAVA_VERSION_MAJOR 8
ENV JAVA_VERSION_MINOR 91
ENV JAVA_VERSION_BUILD 14 

RUN yum install -y \
  curl tar \
  && yum clean all

# install Oracle JRE
RUN mkdir -p /opt \
  && curl --fail --silent --location --retry 3 \
  --header "Cookie: oraclelicense=accept-securebackup-cookie; " \
  http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/server-jre-${JAVA_VERSION_MAJOR}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz \
  | gunzip \
  | tar -x -C /opt \
  && ln -s /opt/jdk1.${JAVA_VERSION_MAJOR}.0_${JAVA_VERSION_MINOR} ${JAVA_HOME}


ENV SONATYPE_WORK /sonatype-work
ENV IQ_VERSION 1.20.0-02

RUN mkdir -p /opt/sonatype/iq-server \
  && curl --fail --silent --location --retry 3 \
    https://download.sonatype.com/clm/server/nexus-iq-server-${IQ_VERSION}-bundle.tar.gz \
  | gunzip \
  | tar x -C /opt/sonatype/iq-server nexus-iq-server-${IQ_VERSION}.jar config.yml 

RUN useradd -r -u 2001 -m -c "iq-server role account" -d ${SONATYPE_WORK} -s /bin/false iq-server

VOLUME ${SONATYPE_WORK}

ENV JVM_OPTIONS -server
EXPOSE 8070
EXPOSE 8071
WORKDIR /opt/sonatype/iq-server
USER iq-server
CMD ${JAVA_HOME}/bin/java \
  ${JVM_OPTIONS} \
  -Ddw.sonatypeWork=${SONATYPE_WORK} \
  -jar nexus-iq-server-${IQ_VERSION}.jar \
  server config.yml