pipeline { 
    agent { label 'nitesh23' }

    environment {
        IMAGE_NAME = 'yourdockerhubusername/react-todo-ui'  // 🔁 Replace with your DockerHub repo
        DOCKERFILE_PATH = 'Dockerfile'  // 🔁 Update if your Dockerfile is in a subfolder (e.g., 'frontend/Dockerfile')
        BUILD_CONTEXT = '.'             // 🔁 Update if needed (e.g., 'frontend')
    }

    stages {

        stage('SCM Checkout') {
            steps {
                echo "Checking out source code..."
                git url: 'https://github.com/Niteshkushwaha9595fbd/Jenkinspipeline.git', branch: 'main'

                // Show directory contents for debugging
                bat 'dir'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image: ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                    
                    // Check if Dockerfile exists before building
                    bat "if not exist %DOCKERFILE_PATH% (echo ERROR: Dockerfile not found at %DOCKERFILE_PATH% && exit /b 1)"

                    // Build Docker image using custom Dockerfile path
                    bat "docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} -f %DOCKERFILE_PATH% %BUILD_CONTEXT%"
                }
            }
        }

        stage('Login to DockerHub & Push') {
            steps {
                script {
                    echo "Logging in and pushing image to DockerHub..."

                    withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Login
                        bat "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"

                        // Tag and push
                        bat "docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest"
                        bat "docker push ${IMAGE_NAME}:${env.BUILD_NUMBER}"
                        bat "docker push ${IMAGE_NAME}:latest"

                        // Logout
                        bat "docker logout"
                    }
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Build failed. Please check logs for details.'
        }
        success {
            echo '✅ Build and push successful!'
        }
    }
}
