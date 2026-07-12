pipeline {

    agent any

    environment {

        IMAGE_NAME = 'jenkins-docker-demo'

        CONTAINER_NAME = 'jenkins-docker-container'

        HOST_PORT = '8081'

    }

    stages {

        stage('checkout code') {

            steps {

                echo 'Downloading website code from GitHub'

                checkout scm

            }

        }

        stage('Build Docker Image') {

            steps {

                echo 'Building Docker image'

                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."

            }

        }

        stage ('stop old container') {

            steps {

                echo 'Stopping old container'

                sh '''

                    docker stop ${CONTAINER_NAME} || true

                    docker rm ${CONTAINER_NAME} || true

                '''

            }

        }

        stage('Run Docker Container') {

            steps {

                echo 'Running Docker container'

                sh "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:80 ${IMAGE_NAME}:${BUILD_NUMBER}"

            }

        }

        stage('Test Website') {

            steps {

                echo 'Testing the deployed website'

                sh '''

                    curl --fail http://localhost:${HOST_PORT}

                    echo "Website test successful"

                '''

            }

        }

    }

    post {

        success {

            echo 'Docker container deployed successfully!'

        }

        failure {

            echo 'Deployment failed. Please check the logs for details.'

        }

    }

}
