FROM maven:3.6.3-jdk-11 AS builder
WORKDIR /workdir/server
COPY pom.xml /workdir/server/pom.xml
RUN mvn dependency:go-offline
COPY src /workdir/server/src
RUN mvn install
RUN mkdir  -p target/depency
WORKDIR /workdir/server/target/dependency

FROM maven:3.6.3-jdk-11 as development
COPY --from=builder /workdir/server/target/project-0.0.1-SNAPSHOT.jar /app/app.jar
ENTRYPOINT [ "java", "-jar", "/app/app.jar" ]

FROM maven:3.6.3-jdk-11 as cache
COPY --from=builder /workdir/server/target/project-0.0.1-SNAPSHOT.jar /app/app.jar
RUN jar -xf /app/app.jar

FROM openjdk:11-jre-slim
EXPOSE 8080
VOLUME /tmp
ARG DEPENDENCY=/app
COPY --from=cache ${DEPENDENCY}/BOOT-INF/lib /app/lib
COPY --from=cache ${DEPENDENCY}/META-INF /app/META-INF
COPY --from=cache ${DEPENDENCY}/BOOT-INF/classes /app
ENTRYPOINT ["java", "-cp", "app:app/lib/*", "com.company.project.Application"]
