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
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker image') {
            when {
                expression { false }
            }
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-creds') {
                        dockerImage.push("${env.BUILD_ID}")
                        dockerImage.push("latest")
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
        always {
            cleanWs()
        }
    }
}
