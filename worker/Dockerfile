FROM maven:3.6.1-jdk-8-slim

WORKDIR /app

COPY . .

RUN mvn package -DskipTests && mv /app/target/worker-jar-with-dependencies.jar /run/worker.jar && rm -rf *

CMD java -jar /run/worker.jar
