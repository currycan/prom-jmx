FROM currycan/jdk8-jmx:0.14.0

COPY app-demo-0.0.1.jar /app/demo.jar

WORKDIR /app

RUN set -ex; mkdir -p /jvm

ENV JVM_GC="-XX:+PrintTenuringDistribution -XX:+PrintGC -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintHeapAtGC -XX:GCLogFileSize=20M -XX:NumberOfGCLogFiles=10 -Xloggc:/jvm/gc-%t.log -XX:+PrintGCApplicationStoppedTime -XX:+PrintGCApplicationConcurrentTime -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/jvm/"
ENV JAVA_OPTS=""

EXPOSE 8080

# 启动服务，运行时会加载 `JMX` 环境变量，`JMX` 变量在基础镜像中指定
ENTRYPOINT java ${JAVA_OPTS} ${JVM_GC} ${JMX} -jar /app/demo.jar
