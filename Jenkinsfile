pipeline {
    agent any

    environment {
        IMAGE_NAME = 'yourdockerhubusername/react-todo-ui'  // Replace with your DockerHub repo
    }

    stages {

        stage('SCM Checkout') {
            steps {
                git url: 'https://github.com/Niteshkushwaha9595fbd/ReactTodoUIMonolith.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
                }
            }
        }

        stage('Login to DockerHub & Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh "docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest"
                        sh "docker push ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                        sh "docker push ${IMAGE_NAME}:latest"
                        sh 'docker logout'
                    }
                }
            }
        }
    }

    post {
        failure {
            echo 'Build failed.'
        }
        success {
            echo 'Build and push successful.'
        }
    }
}
