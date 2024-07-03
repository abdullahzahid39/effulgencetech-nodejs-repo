pipeline {
    agent any

    environment {
        IMAGE_REPO_NAME = "https://github.com/abdullahzahid39/effulgencetech-nodejs-repo"
        CONTAINER_NAME = "effulgencetech-nodejs-cont"
    }

    stages {
        // Check if Docker is available
        stage('Check Docker') {
            steps {
                script {
                    def dockerVersion = sh(returnStdout: true, script: 'docker --version || true').trim()
                    if (dockerVersion.isEmpty()) {
                        echo 'Docker is not installed. Installing Docker...'
                        sh 'curl -fsSL https://get.docker.com -o get-docker.sh'
                        sh 'sudo sh get-docker.sh'
                        sh 'sudo usermod -aG docker $USER'
                        echo 'Docker installed successfully.'
                    } else {
                        echo "Docker is already installed: ${dockerVersion}"
                    }
                }
            }
        }

        // Cloning the project repository from GitHub
        stage('Git checkout') {
            steps {
                echo 'Cloning project codebase...'
                git branch: 'main', url: 'https://github.com/abdullahzahid39/effulgencetech-nodejs-repo.git'
            }
        }

        // Building the Docker image and tagging it with the build number
        stage('Build-Image') {
            steps {
                script {
                    def dockerImage = docker.build("${IMAGE_REPO_NAME}:${BUILD_NUMBER}")
                    echo 'Docker image built:'
                    sh 'docker images'
                }
            }
        }

        // Running the Docker container from the built image
        stage('Build-Container') {
            steps {
                sh "docker run --name ${CONTAINER_NAME}-${BUILD_NUMBER} -p 8089:8080 -d ${IMAGE_REPO_NAME}:${BUILD_NUMBER}"
                echo 'Docker container running:'
                sh 'docker ps'
            }
        }
    }

    // Optional: Post actions to notify on build success or failure
    post {
        always {
            echo 'Pipeline execution completed!'
            // Uncomment and configure the following line if you want to send notifications, e.g., via Slack
            // slackSend channel: '#developers', color: COLOR_MAP[currentBuild.currentResult], message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \nMore info at: ${env.BUILD_URL}"
        }
    }
}
