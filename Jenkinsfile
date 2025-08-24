pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')   // DockerHub creds ID in Jenkins
        DOCKERHUB_USER = 'prasannakakaraparthi2303'               // Replace with your DockerHub username
        GITHUB_REPO = 'https://github.com/deviprasannakakaraparthi/crud-dd-task-mean-app.git'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master',
                    url: "${GITHUB_REPO}",
                    credentialsId: 'github-cred'   // GitHub creds ID in Jenkins
            }
        }

        stage('Build Backend Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_USER}/crud-dd-task-mean-app-backend:latest ./backend"
                }
            }
        }

        stage('Build Frontend Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKERHUB_USER}/crud-dd-task-mean-app-frontend:latest ./frontend"
                }
            }
        }

        stage('Push Images to DockerHub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh """
                            echo $PASS | docker login -u $USER --password-stdin
                            docker push ${DOCKERHUB_USER}/crud-dd-task-mean-app-backend:latest
                            docker push ${DOCKERHUB_USER}/crud-dd-task-mean-app-frontend:latest
                        """
                    }
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                script {
                    sh """
                        docker-compose down || true
                        docker-compose pull
                        docker-compose up -d
                    """
                }
            }
        }
    }
}

