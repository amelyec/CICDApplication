pipeline {
    agent any

    environment {
        JAVA_HOME = tool name: 'JDK 1.8', type: 'jdk'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"

        DOCKER_HUB_USERNAME = 'werelay' // Your Docker Hub username
        DOCKER_HUB_CREDENTIALS_ID = 'docker-hub-creds' // Jenkins credentials ID
    }

    tools {
        maven 'Maven 3.9.9'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/amelyec/CICDApplication', branch: 'main'
            }
        }

        stage('Build and Package') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build and Push Docker Image with Jib') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_HUB_CREDENTIALS_ID}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        mvn compile com.google.cloud.tools:jib-maven-plugin:2.2.0:build \\
                            -Pdeploy-docker \\
                            -Djib.to.auth.username=$DOCKER_USER \\
                            -Djib.to.auth.password=$DOCKER_PASS
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Docker image built and pushed to Docker Hub successfully!"
        }
        failure {
            echo "❌ Build or image push failed."
        }
    }
}
