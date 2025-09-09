#Stage 1: Maven build environment
FROM maven:3.9.6-eclipse-temurin-17  AS build-environment

WORKDIR /app

#copied pom.xml and download dependencies mentioned in pom.xml
COPY pom.xml ./

#copied source code and create .jar file
COPY src ./src 

#Build the .jar file
RUN mvn clean package -DskipTests

#Stage 2: Build Spring Boot application
#started with smaller JDK image to compile .jar file
FROM eclipse-temurin:17-jdk-alpine AS build 

WORKDIR /app

#This line takes the JAR from target/ in Stage 1 and puts it in the working directory of the current stage.This is because Maven always outputs compiled artifacts to target/ by default.
COPY --from=build-environment /app/target/*.jar ./   

#Stage 3: JRE runtime [final image only has the JAR and JRE]
#Choose lightweight JRE image
FROM eclipse-temurin:17-jre-alpine

WORKDIR /app

#copied .jar file from stage 2 to current workdir of containerConnection closed
COPY --from=build /app/*.jar ./app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
