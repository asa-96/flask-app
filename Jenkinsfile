pipeline {
    agent any 
    triggers {
        GenericTrigger(
            genericVariables: [
                [ key: 'SOURCE_BRANCH', value: '$.pull_request.head.ref' ],
                [ key: 'TARGET_BRANCH', value: '$.pull_request.base.ref' ]
            ],
            causeString: 'Pull Request Trigger',
            printContributedVariables: true,
            token: 'test123'
        )
    }

    environment {
        DOCKER_IMAGE = 'asa96/flask-docker-app'
    }

    stages {
        stage('Checkout Source Branch') {
            steps {
                script {
                    // Explicitly checkout the source branch from the specified repository
                    checkout([$class: 'GitSCM', 
                             branches: [[name: 'main']], 
                             userRemoteConfigs: [[url: 'https://github.com/asa-96/flask-app.git']]])
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            when {
                expression { false }
            }
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-creds') {
                        dockerImage.push("${DOCKER_IMAGE}:${env.BUILD_ID}")
                        dockerImage.push("${DOCKER_IMAGE}:latest")
                    }
                }
            }
        }

        stage('Deploy to Docker') {
            when {
                expression { false }
            }
            steps {
                script {
                    dockerImage.run("-p 5000:5000")
                }
            }
        }
    }

    post {
        success {
            script {
                // Perform actions after a successful build
                echo 'Pipeline succeeded! Sending notifications...'
            }
        }
        
        always {
            // Clean workspace after each run
            cleanWs()
        }
    }
}
