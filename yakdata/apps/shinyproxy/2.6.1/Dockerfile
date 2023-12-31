FROM ubuntu:focal

LABEL version=2.6.1

# from https://github.com/adoptium/containers/blob/main/11/jdk/ubuntu/Dockerfile.releases.full

ENV LANG='en_US.UTF-8' LANGUAGE='en_US:en' LC_ALL='en_US.UTF-8'

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata curl ca-certificates fontconfig locales \
    && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
    && locale-gen en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

ENV JAVA_VERSION jdk-11.0.12+7

RUN set -eux; \
    ARCH="$(dpkg --print-architecture)"; \
    case "${ARCH}" in \
       aarch64|arm64) \
         ESUM='105bdc12fcd54c551e8e8ac96bc82412467244c32063689c41cee29ceb7452a2'; \
         BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.12%2B7/OpenJDK11U-jdk_aarch64_linux_hotspot_11.0.12_7.tar.gz'; \
         ;; \
       armhf|arm) \
         ESUM='85929246a9d60ed90e2385ea3f4857798c77019c5100f00189c1dd0a093abea7'; \
         BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.12%2B7/OpenJDK11U-jdk_arm_linux_hotspot_11.0.12_7.tar.gz'; \
         ;; \
       ppc64el|powerpc:common64) \
         ESUM='234a9bafe029ea6cab5d46f9617b5d016a29faa187a42081d0e066f23647b7e5'; \
         BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.12%2B7/OpenJDK11U-jdk_ppc64le_linux_hotspot_11.0.12_7.tar.gz'; \
         ;; \
       s390x|s390:64-bit) \
         ESUM='99c1c61e9d67b01a649687189e5a00f89a2371dc1bfed06bb078d127edfa7995'; \
         BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.12%2B7/OpenJDK11U-jdk_s390x_linux_hotspot_11.0.12_7.tar.gz'; \
         ;; \
       amd64|i386:x86-64) \
         ESUM='8770f600fc3b89bf331213c7aa21f8eedd9ca5d96036d1cd48cb2748a3dbefd2'; \
         BINARY_URL='https://github.com/adoptium/temurin11-binaries/releases/download/jdk-11.0.12%2B7/OpenJDK11U-jdk_x64_linux_hotspot_11.0.12_7.tar.gz'; \
         ;; \
       *) \
         echo "Unsupported arch: ${ARCH}"; \
         exit 1; \
         ;; \
    esac; \
    curl -LfsSo /tmp/openjdk.tar.gz ${BINARY_URL}; \
    echo "${ESUM} */tmp/openjdk.tar.gz" | sha256sum -c -; \
    mkdir -p /opt/java/openjdk; \
    cd /opt/java/openjdk; \
    tar -xf /tmp/openjdk.tar.gz --strip-components=1; \
    rm -rf /tmp/openjdk.tar.gz;

ENV JAVA_HOME=/opt/java/openjdk \
    PATH="/opt/java/openjdk/bin:$PATH"

RUN echo Verifying install ... \
    && echo javac --version && javac --version \
    && echo java --version && java --version \
    && echo Complete.

# end from https://github.com/adoptium/containers/blob/main/11/jdk/ubuntu/Dockerfile.releases.full

RUN /usr/bin/apt-get -y update \
     && DEBIAN_FRONTEND=noninteractive apt-get -y upgrade \
     && /usr/bin/apt-get -y autoremove \
     && /usr/bin/apt-get -y autoclean \
     && /usr/bin/apt-get -y install curl software-properties-common apt-transport-https \
     && /usr/bin/apt-get -y update \
     && DEBIAN_FRONTEND=noninteractive apt-get -y upgrade \
     && /usr/bin/apt-get -y autoremove \
     && /usr/bin/apt-get -y autoclean

WORKDIR /opt/shinyproxy

RUN /usr/bin/apt-get -y install wget  \
     && wget https://shinyproxy.io/downloads/shinyproxy-2.6.1.jar \
     && /usr/bin/apt-get -y install curl software-properties-common apt-transport-https \
     && apt-get -y update \
     && apt-get -y dist-upgrade



ENV SHINY_USER_GROUP=shinyproxy
ENV SHINY_USER_GROUP_ID=998

RUN groupadd -g $SHINY_USER_GROUP_ID $SHINY_USER_GROUP
RUN useradd -c 'shinyproxy user' -m -d /home/shinyproxy -s /usr/sbin/nologin -u $SHINY_USER_GROUP_ID -g $SHINY_USER_GROUP_ID $SHINY_USER_GROUP

USER shinyproxy

CMD ["java", "-jar", "shinyproxy-2.6.1.jar"]
