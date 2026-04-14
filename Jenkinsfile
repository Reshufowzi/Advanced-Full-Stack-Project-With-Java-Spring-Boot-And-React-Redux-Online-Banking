pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO_BACKEND = "reshma0209/backend-app"
        DOCKER_HUB_REPO_FRONTEND = "reshma0209/frontend-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Build Backend') {
            steps {
                dir('Online Banking App Spring Boot') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                dir('Online Banking App Spring Boot') {
                    sh 'docker build -t $DOCKER_HUB_REPO_BACKEND:$IMAGE_TAG .'
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('demo-bank-redux') {
                    sh 'docker build -t $DOCKER_HUB_REPO_FRONTEND:$IMAGE_TAG .'
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_HUB_REPO_BACKEND:$IMAGE_TAG'
                    sh 'docker push $DOCKER_HUB_REPO_FRONTEND:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop backend || true
                docker rm backend || true
                docker run -d -p 8080:8080 --name backend reshma0209/backend-app:latest

                docker stop frontend || true
                docker rm frontend || true
                docker run -d -p 3000:80 --name frontend reshma0209/frontend-app:latest
                '''
            }
        }
    }
}
