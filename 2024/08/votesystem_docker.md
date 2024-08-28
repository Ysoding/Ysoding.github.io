
> 为什么做此？因为真的有人用的此项目，会来联系我问问题，所以可以考虑部署运行方便。

docker需要配置代理

## Dockerfile

首先就是通过编写Dockerfile来构建image，  
内部就是通过maven构建jar包来运行。

maven拉镜像的时候可能也需要代理，或者换源，所以我把maven中的settings.xml配置copy到image里 则可以在里面配置源，设置代理等等。

```Dockerfile
FROM maven:3.8.5-openjdk-11 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
COPY settings.xml /usr/share/maven/conf/settings.xml
RUN mvn clean package -DskipTests -X


FROM openjdk:11-jdk
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]

```

## Docker-compose

因为需要等待mysql启动完成后再启动service，否则service连接不上mysql，则日志  
所以需要加上health check

```yaml
version: "3.8"

services:
  db:
    image: mysql:5.7
    container_name: mysql-db
    environment:
      MYSQL_ROOT_PASSWORD: 123456
      MYSQL_DATABASE: vote
      MYSQL_USER: ultraman
      MYSQL_PASSWORD: 123456
    volumes:
      - db_data:/var/lib/mysql
      - ./vote.sql:/docker-entrypoint-initdb.d/vote.sql
    ports:
      - "3306:3306"
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost", "-p123456"]
      interval: 10s
      timeout: 5s
      retries: 5
      start_period: 30s

  app:
    build: .
    container_name: vote-system-app
    depends_on:
      db:
        condition: service_healthy
    ports:
      - "8080:8080"

volumes:
  db_data:

```
