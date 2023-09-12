pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from your Git repository
                script {
                    checkout scm
                }
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                script {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Dockerize') {
            steps {
                // Build a Docker image of your React app
                script {
                    def dockerImage = docker.build("jayacontreg.azurecr.io/npm-image:${env.BUILD_ID}")

                    // Use Azure Container Registry credentials
                    withCredentials([usernamePassword(credentialsId: 'acr-credentials', usernameVariable: 'ACR_USERNAME', passwordVariable: 'ACR_PASSWORD')]) {
                        dockerImage.withRegistry("https://jayacontreg.azurecr.io", 'ACR_USERNAME', 'ACR_PASSWORD')
                    }

                    dockerImage.push()
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
