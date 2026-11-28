# 运行时用 JRE（更轻量）
FROM eclipse-temurin:17-jre

# 创建固定 UID 的非 root 用户，并创建 Nacos 客户端所需的 home 目录
RUN groupadd -g 1001 appgroup && \
    useradd -r -u 1001 -g appgroup -m appuser

WORKDIR /app

# 设置时区 + 安装 curl
ENV TZ=Asia/Shanghai
RUN apt-get update \
    && apt-get install -y --no-install-recommends curl \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && rm -rf /var/lib/apt/lists/*

COPY target/AiService-0.0.1-SNAPSHOT.jar app.jar

COPY src/main/resources/docs/ /init-docs/

RUN mkdir -p /data/docs /home/appuser/nacos/naming/public && \
    chown -R appuser:appgroup /app /init-docs /data /home/appuser

USER appuser

EXPOSE 8105

VOLUME /tmp

ENTRYPOINT ["sh", "-c", "echo 'Current User:' $(whoami); cp -rn /init-docs/. /data/docs/ || true; ls -l /data/docs/; exec java $JAVA_OPTS -jar /app/app.jar --spring.profiles.active=${SPRING_PROFILES_ACTIVE:-prod}"]