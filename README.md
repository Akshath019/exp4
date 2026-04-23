

# CI/CD & Docker Experiments

This repository contains multiple experiments covering Maven, Docker, Jenkins pipelines, volumes, and containerization.

---

## Experiment 20: Maven Web App + CI/CD Pipeline

### Generate Project

mvn archetype:generate

Select:
maven-archetype-webapp

Provide:
GroupId: com.example  
ArtifactId: cicd-demo  

cd cicd-demo

---

### Update pom.xml

<dependencies>
  <dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
  </dependency>

  <dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
  </dependency>
</dependencies>

---

### Create Servlet

mkdir -p src/main/java/com/example

HelloServlet.java

package com.example;

import java.io.*;
import javax.servlet.*;
import javax.servlet.http.*;

public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest req, HttpServletResponse res)
            throws IOException {

        res.setContentType("text/html");
        res.getWriter().println("<h1>CI/CD Working</h1>");
    }
}

---

### Update web.xml

<servlet>
  <servlet-name>Hello</servlet-name>
  <servlet-class>com.example.HelloServlet</servlet-class>
</servlet>

<servlet-mapping>
  <servlet-name>Hello</servlet-name>
  <url-pattern>/hello</url-pattern>
</servlet-mapping>

---

### Create Test

mkdir -p src/test/java/com/example

TestSample.java

import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class TestSample {
    @Test
    void test() {
        assertEquals(2, 1 + 1);
    }
}

---

### Build Project

mvn clean package

---

### Dockerfile

FROM tomcat:9-jdk17  
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war

---

### Run Docker

docker build -t cicd-app .  
docker run -d -p 8087:8080 cicd-app  

---

### Jenkins Pipeline

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Run') {
            steps {
                sh 'docker build -t cicd-app .'
                sh 'docker run -d -p 8087:8080 cicd-app'
            }
        }
    }
    post {
        always {
            junit '**/target/surefire-reports/*.xml'
        }
    }
}

---

## Experiment 18: Java Docker Image

### Dockerfile

FROM eclipse-temurin:17-jdk  
WORKDIR /app  
COPY . /app  
RUN javac Grade.java  
CMD ["java", "Grade"]  

### Commands

docker build -t java-app .  
docker images  
docker login  
docker tag java-app yourusername/java-app:v1  
docker push yourusername/java-app:v1  

---

## Experiment 19: Simple Jenkins Docker Pipeline

pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t my-app .'
            }
        }

        stage('Run Container') {
            steps {
                bat 'docker run my-app'
            }
        }
    }
}

---

## Experiment 17: Docker Volumes

docker run -d -p 8080:80 nginx  

docker volume create myvolume  

docker run -it -v myvolume:/data ubuntu bash  
cd /data  
echo "Hello from Docker Volume" > test.txt  
exit  

docker run -it -v myvolume:/data ubuntu bash  
cat /data/test.txt  
exit  

docker run -d -p 8081:80 -v myvolume:/usr/share/nginx/html nginx  

docker run -it -v myvolume:/usr/share/nginx/html ubuntu bash  
echo "<h1>Docker Volume Working</h1>" > /usr/share/nginx/html/index.html  
exit  

---

## Experiment 16: Python Docker Container

### app.py

from http.server import SimpleHTTPRequestHandler, HTTPServer

PORT = 5020

class MyHandler(SimpleHTTPRequestHandler):
    def do_GET(self):
        self.send_response(200)
        self.send_header("Content-type", "text/html")
        self.end_headers()
        self.wfile.write(b"<h1>Hello from Docker Python Container</h1>")

server = HTTPServer(("0.0.0.0", PORT), MyHandler)
print(f"Server running on port {PORT}...")
server.serve_forever()

---

### Dockerfile

FROM python:3.9-slim  
WORKDIR /app  
COPY app.py .  
EXPOSE 5020  
CMD ["python", "app.py"]  

---

### Commands

docker build -t my-python-app .  
docker images  
docker run -d -p 5020:5020 my-python-app  

---

## Experiment 14: JaCoCo Code Coverage Plugin

<build>
    <plugins>

        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.11</version>

            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>

                <execution>
                    <id>report</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>

    </plugins>
</build>

---

---
tags

---
git tag v1.0
git tag -a v1.2 -m "version 1.2"
git show v1.0
git tag -l 1v.*
git tag v2.0
git tag push --tags
git tag -d v1.0
git push origin -d v.10

