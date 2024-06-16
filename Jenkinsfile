pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'asa96/flask-docker-app'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/asa-96/flask-app.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        // stage('Push Docker Image') {
        //     steps {
        //         script {
        //             withDockerRegistry(credentialsId: 'docker-creds') {
        //                 dockerImage.push("${env.BUILD_ID}")
        //                 dockerImage.push("latest")
        //             }
        //         }
        //     }
        // }

        // stage('Deploy to Docker') {
        //     steps {
        //         script {
        //             dockerImage.run("-p 5000:5000")
        //         }
        //     }
        // }
    }

    post {
        always {
            cleanWs()
        }
    }
}
