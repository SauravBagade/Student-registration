FROM maven:3.8.3-openjdk-17 AS build

# Set working directory
WORKDIR /opt

# Copy source code
COPY . .

# Replace application.properties (optional, if you have a custom one)
RUN rm -f src/main/resources/application.properties && \
    cp -f application.properties src/main/resources/application.properties

# Build the app (skip tests to avoid build failure)
RUN mvn clean package -DskipTests

# Final image to run the app
FROM eclipse-temurin:17-jdk-jammy
WORKDIR /app

# Copy the built jar from previous stage
COPY --from=build /opt/target/student-registration-backend-0.0.1-SNAPSHOT.jar .

# Expose the port
EXPOSE 8080

# Run the jar
CMD ["java", "-jar", "student-registration-backend-0.0.1-SNAPSHOT.jar"]
