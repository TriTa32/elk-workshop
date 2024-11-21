# Multi-stage build
FROM gradle:4.10.3-jdk8-alpine AS build

# Set working directory
WORKDIR /home/gradle/project

# Set environment
ENV GRADLE_USER_HOME /home/gradle/project

# Switch to root for permissions
USER root

# Copy only build files first for better caching
COPY build.gradle settings.gradle gradle.properties ./
COPY gradle ./gradle
COPY gradlew ./

# Download dependencies
RUN chmod +x ./gradlew
RUN ./gradlew --no-daemon build --info --stacktrace || true

# Copy entire project
COPY . .

# Build the project, skipping tests
RUN ./gradlew --no-daemon build -x test

# Second stage
FROM openjdk:8-jre-alpine

# Set working directory
WORKDIR /app

# Copy built jar from previous stage
COPY --from=build /home/gradle/project/build/libs/*.jar app.jar

# Expose port
EXPOSE 8080

# Entry point
ENTRYPOINT ["java", "-jar", "app.jar"]