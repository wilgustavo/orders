FROM bellsoft/liberica-openjdk-debian:23 AS builder
WORKDIR /app
COPY . .
RUN --mount=type=cache,target=/root/.m2 ./mvnw clean package -DskipTests

FROM bellsoft/liberica-openjdk-debian:23 AS optimizer
WORKDIR /extracted
COPY --from=builder /app/target/*.jar app.jar
RUN java -Djarmode=tools -jar app.jar extract --layers --launcher

FROM bellsoft/liberica-openjre-alpine-musl:23
RUN addgroup -S spring && adduser -S spring -G spring
WORKDIR /application
COPY --from=optimizer /extracted/app/dependencies/ ./
COPY --from=optimizer /extracted/app/spring-boot-loader/ ./
COPY --from=optimizer /extracted/app/snapshot-dependencies/ ./
COPY --from=optimizer /extracted/app/application/ ./

USER spring
EXPOSE 8080
EXPOSE 8081
ENTRYPOINT ["java", "org.springframework.boot.loader.launch.JarLauncher"]
