// Jenkinsfile

pipeline {
    agent any

    stages {
        stage('Pulling latest code') {
            steps {
                script {
                    dir('D:\\workspace6') {
                        // Check if the Git repository is already initialized
                        if (!fileExists(".git")) {
                            bat 'git init'
                            bat 'git remote add origin https://github.com/jayadharshini-k/npm-pipeline.git'
                        } else {
                            echo "Git repository already initialized."
                        }
                        // Always perform git pull to get the latest code
                        bat 'git pull origin main'
                    }

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
