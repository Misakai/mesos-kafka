FROM debian:wheezy
MAINTAINER Roman Atachiants "roman@misakai.com"

# Deploy workdir
WORKDIR /app

# The dependencies needed for the compilation process, they will be deleted once the docker image is baked
ENV SETUP_TOOLS="git curl unzip autoconf libtool automake build-essential gettext python"
ENV KAFKA_DIST="https://archive.apache.org/dist/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz"
ENV GRADLE_DIST="https://services.gradle.org/distributions/gradle-2.3-all.zip"
ENV GRADLE_HOME=/opt/gradle-2.3-all

# Paths
ENV PATH=$PATH:$GRADLE_HOME/bin
ENV MESOS_NATIVE_JAVA_LIBRARY=/usr/local/lib/libmesos.so

# OpenJDK JRE that we shall keep 
RUN apt-get -y install openjdk-7-jre-headless --no-install-recommends --no-install-suggests
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
	&& rm -rf /app/g \
	&& mkdir /app


CMD ["/bin/bash", "/app/kafka-mesos.sh", "scheduler"]