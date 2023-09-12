pipeline {
    agent any

    stages {
        stage('Pulling latest code') {
            steps {
                script {
                    dir('D:\\npm-pipeline') {
                        // Initialize Git repository if not already initialized
                        if (!fileExists(".git")) {
                            bat 'git init'
                            bat 'git remote add origin https://github.com/jayadharshini-k/npm-pipeline.git'
                        }
                        
                        // Always fetch the latest code from the remote repository
                        bat 'git fetch origin'
                        
                        // Check out the correct branch (e.g., 'main')
                        bat 'git checkout main'
                        
                        // Pull the latest code from the branch
                        bat 'git pull origin main'
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                script {
                    bat 'npm install'
                    bat 'npm run build'
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
