
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'jenkins-cicd-app'
        CONTAINER_NAME = 'jenkins-cicd-container'
        HOST_PORT = '8080'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker run -d --name ${CONTAINER_NAME}-test \
                        -p 18080:80 ${IMAGE_NAME}:${BUILD_NUMBER}

                    sleep 3

                    curl --fail http://localhost:18080/

                    docker rm -f ${CONTAINER_NAME}-test
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${CONTAINER_NAME} || true

                    docker run -d \
                        --name ${CONTAINER_NAME} \
                        --restart unless-stopped \
                        -p ${HOST_PORT}:80 \
                        ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }

        always {
            sh '''
                docker rm -f ${CONTAINER_NAME}-test || true
            '''
        }
    }
}
