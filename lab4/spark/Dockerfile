FROM eclipse-temurin:11-jre-focal

ARG SPARK_VERSION=3.5.0
ARG HADOOP_PROFILE=hadoop3

# Установка зависимостей
RUN apt-get update && \
    apt-get install -y --no-install-recommends wget ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Создание пользователя с домашней директорией
RUN groupadd -r spark && useradd -rm -g spark spark

# Установка Spark
RUN wget -q "https://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-${HADOOP_PROFILE}.tgz" && \
    tar -xzf spark-${SPARK_VERSION}-bin-${HADOOP_PROFILE}.tgz -C /opt && \
    ln -s /opt/spark-${SPARK_VERSION}-bin-${HADOOP_PROFILE} /opt/spark && \
    mkdir -p /opt/spark/work-dir && \
    chown -R spark:spark /opt/spark && \
    rm spark-${SPARK_VERSION}-bin-${HADOOP_PROFILE}.tgz

ENV SPARK_HOME=/opt/spark
ENV PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin

USER spark
WORKDIR /home/spark

VOLUME ["/opt/spark/work-dir"]
