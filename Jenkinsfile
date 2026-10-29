pipeline {
    agent any

    parameters {
        string(name: 'IMAGE_NAME', defaultValue: '51.250.37.218:8082/homework11', trim: true)
        string(name: 'IMAGE_TAG', defaultValue: 'v1.0', trim: true)
    }

    environment {
        IMAGE_NAME = "${params.IMAGE_NAME}"
        IMAGE_TAG = "${params.IMAGE_TAG}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build image') {
            steps {
                writeFile file: 'Dockerfile.homework11',
                text: '''FROM maven:3.9-eclipse-temurin-21 AS build
                                    WORKDIR /app
                                    COPY . .
                                    RUN mvn -B package -DskipTests
                                    
                                    FROM eclipse-temurin:21-jre
                                    WORKDIR /app
                                    COPY --from=build /app/target/*.jar app.jar
                                    EXPOSE 8080
                                    ENTRYPOINT ["java", "-jar", "app.jar"]
'''
                sh 'docker build -f Dockerfile.homework11 -t "$IMAGE_NAME:$IMAGE_TAG" .'
            }
        }

        stage('Push image') {
            steps {
                sh 'docker push "$IMAGE_NAME:$IMAGE_TAG"'
            }
        }

        stage('Deploy') {
            steps {
                sh '''docker pull "$IMAGE_NAME:$IMAGE_TAG"
                    docker rm -f homework11-app || true
                    docker run -d --name homework11-app -p 8083:8080 "$IMAGE_NAME:$IMAGE_TAG"
                    '''
            }
        }
    }
}
