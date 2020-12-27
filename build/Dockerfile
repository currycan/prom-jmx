FROM currycan/oracle-jdk:8u201

ARG VERSION=0.14.0

ADD https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/${VERSION}/jmx_prometheus_javaagent-${VERSION}.jar /jmx/prometheus_javaagent.jar
COPY jmx-cfg.yml /jmx/

ENV JMX="-javaagent:/jmx/prometheus_javaagent.jar=20000:/jmx/jmx-cfg.yml"

EXPOSE 20000
