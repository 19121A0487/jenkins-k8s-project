# Use a lightweight JDK 17 image
FROM openjdk:17-jdk-slim

# Set the working directory inside the container
WORKDIR /app

# Copy the .jar file from your Maven target folder to the container
# Note: Ensure the version matches your pom.xml (1.0-SNAPSHOT)
COPY target/sample-app-1.0-SNAPSHOT.jar app.jar

# Command to run the application
ENTRYPOINT ["java", "-jar", "app.jar"]
