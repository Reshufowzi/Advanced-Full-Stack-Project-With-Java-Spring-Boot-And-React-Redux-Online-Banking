pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    environment {
        DOCKER_HUB_REPO_BACKEND = "reshma0209/backend-app"
        DOCKER_HUB_REPO_FRONTEND = "reshma0209/frontend-app"
        IMAGE_TAG = "${BUILD_NUMBER}"   // ✅ dynamic tag
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
                    sh '''
                    docker build -t $DOCKER_HUB_REPO_BACKEND:$IMAGE_TAG .
                    docker tag $DOCKER_HUB_REPO_BACKEND:$IMAGE_TAG $DOCKER_HUB_REPO_BACKEND:latest
                    '''
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                dir('demo-bank-redux') {
                    sh '''
                    docker build -t $DOCKER_HUB_REPO_FRONTEND:$IMAGE_TAG .
                    docker tag $DOCKER_HUB_REPO_FRONTEND:$IMAGE_TAG $DOCKER_HUB_REPO_FRONTEND:latest
                    '''
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin

                    docker push $DOCKER_HUB_REPO_BACKEND:$IMAGE_TAG
                    docker push $DOCKER_HUB_REPO_BACKEND:latest

                    docker push $DOCKER_HUB_REPO_FRONTEND:$IMAGE_TAG
                    docker push $DOCKER_HUB_REPO_FRONTEND:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop backend || true
                docker rm backend || true
                docker run -d -p 8081:8080 --name backend $DOCKER_HUB_REPO_BACKEND:latest

                docker stop frontend || true
                docker rm frontend || true
                docker run -d -p 3000:80 --name frontend $DOCKER_HUB_REPO_FRONTEND:latest
                '''
            }
        }
    }
}
