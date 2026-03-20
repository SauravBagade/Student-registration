FROM maven:3.8.3-openjdk-17 AS build
WORKDIR /opt
COPY . .
RUN mvn clean package -DskipTests
FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app
COPY --from=build /opt/target/student-registration-backend-0.0.1-SNAPSHOT.jar .
EXPOSE 8080
CMD ["java", "-jar", "student-registration-backend-0.0.1-SNAPSHOT.jar"]
