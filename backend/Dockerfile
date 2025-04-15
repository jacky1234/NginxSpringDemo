# 我们使用 eclipse-temurin:17-jdk-alpine 作为基础镜像，它基于 Alpine Linux
FROM eclipse-temurin:17-jdk-alpine

WORKDIR /app

# 安装 wget 用于健康检查， Alpine Linux 的包管理器（Package Manager）。 这里的 wget 为要安装的软件包名称
RUN apk add --no-cache wget

# 复制构建文件
COPY build.gradle settings.gradle gradlew ./
COPY gradle ./gradle
COPY src ./src

# 设置 Gradle 属性以解决 TLS 问题
ENV GRADLE_OPTS="-Dhttps.protocols=TLSv1.2,TLSv1.3 -Djavax.net.ssl.trustStore=/etc/ssl/certs/java/cacerts -Djavax.net.ssl.trustStorePassword=changeit"

# 构建应用
RUN ./gradlew build -x test --no-daemon

# 设置工作目录为构建输出目录
WORKDIR /app/build/libs

# 查找并设置 JAR 文件（排除 -plain.jar）
RUN JAR_FILE=$(ls *.jar | grep -v plain) && echo "Found JAR file: $JAR_FILE" && mv $JAR_FILE app.jar

EXPOSE 8080

# 使用固定的 JAR 文件名运行
CMD ["java", "-jar", "app.jar"] 