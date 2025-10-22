# === BASE IMAGE ===
FROM docker.elastic.co/logstash/logstash:8.19.5

# === INSTALL DEPENDENCIES ===
USER root
RUN apt-get update && \
    apt-get install -y postgresql-client curl && \
    rm -rf /var/lib/apt/lists/*

# === CREATE DIRECTORIES ===
RUN mkdir -p /usr/share/logstash/config \
    /usr/share/logstash/pipeline \
    /usr/share/logstash/logs \
    /usr/share/logstash/data/queue && \
    chown -R logstash:logstash /usr/share/logstash && \
    chmod -R 775 /usr/share/logstash/data

# === COPY CONFIGURATION FILES ===
COPY pipeline/logstash.conf /usr/share/logstash/pipeline/logstash.conf
COPY config/logstash.yml /usr/share/logstash/config/logstash.yml
COPY config/pipelines.yml /usr/share/logstash/config/pipelines.yml

# === INSTALL JDBC OUTPUT PLUGIN ===
RUN /usr/share/logstash/bin/logstash-plugin install logstash-output-jdbc

# === DOWNLOAD POSTGRESQL JDBC DRIVER ===
RUN curl -L -o /usr/share/logstash/logstash-core/lib/jars/postgresql-42.7.8.jar \
    https://jdbc.postgresql.org/download/postgresql-42.7.8.jar

# === SET PERMISSIONS ===
RUN chown -R logstash:logstash /usr/share/logstash

# === RETURN TO LOGSTASH USER ===
USER logstash

# === ENVIRONMENT ===
ENV LANG C.UTF-8

# === STARTUP ===
CMD ["/usr/share/logstash/bin/logstash", "-f", "/usr/share/logstash/pipeline/logstash.conf"]

