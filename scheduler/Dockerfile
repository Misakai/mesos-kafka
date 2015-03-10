FROM debian:wheezy
MAINTAINER Roman Atachiants "roman@misakai.com"

# Deploy workdir
WORKDIR /app

# The dependencies needed for the compilation process, they will be deleted once the docker image is baked
ENV SETUP_TOOLS="git curl unzip autoconf libtool automake build-essential gettext python"
ENV KAFKA_DIST="https://archive.apache.org/dist/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz"
ENV GRADLE_DIST="https://downloads.gradle.org/distributions/gradle-2.3-all.zip"
ENV GRADLE_HOME=/opt/gradle-2.3-all

# Paths
ENV PATH=$PATH:$GRADLE_HOME/bin
ENV MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so
ENV JAVA_HOME /usr/lib/jvm/java-8-oracle

# auto validate license
RUN echo oracle-java8-installer shared/accepted-oracle-license-v1-1 select true | /usr/bin/debconf-set-selections

# update repos and install java
RUN echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee /etc/apt/sources.list.d/webupd8team-java.list \
	&& echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu trusty main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list \
	&& apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886 \
	&& apt-get update \
	&& apt-get install oracle-java8-installer -y  --force-yes \
	&& apt-get clean

# Install Kafka 
RUN apt-get update \
	&& apt-get install -y $SETUP_TOOLS \
	&& mkdir /app/g \
	&& curl -o /app/g/gradle.zip $GRADLE_DIST \
	&& unzip /app/g/gradle.zip \
	&& mv /app/g/gradle* /opt/ \
	&& git clone https://github.com/mesos/kafka \
	&& cd kafka \
	&& bash /app/kafka/gradlew jar \
	&& wget $KAFKA_DIST \
	&& apt-get remove -y --purge $SETUP_TOOLS \
	&& apt-get autoremove -y \
	&& rm -rf /app/g

CMD ["/bin/bash", "/app/kafka-mesos.sh", "scheduler"]