pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'roberta-bot'
        DOCKER_IMAGE_TAG = '0.1.0'
        DOCKERHUB_USERNAME = 'deepanshurawat6'
    }

    stages {
        stage ('Starting Build Pipeline') {
            steps {
                echo 'Initiating build pipeline'
            }
        }

        stage ('Connecting to DockerHub') {
            steps {
                withCredentials([
                    usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')
                ]){
                    sh 'docker login --username $USERNAME --password $PASSWORD'
                }
            }
        }

        stage ('Building Docker Image') {
            steps {
                sh '''
                    docker build -t ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} .
                '''
            }
        }

        stage ('Tagging Docker Image') {
            steps {
                sh ''' 
                    docker tag ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} ${DOCKERHUB_USERNAME}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}
                '''
            }
        }

        stage ('Pushing Docker Image') {
            steps {
                sh ''' 
                    docker push ${DOCKERHUB_USERNAME}/${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}
                '''
            }
            post {
               always {
                 sh '''
                 docker image prune -f -a --filter "until=240h"
                 '''
               }

            }
        }

        stage ('Finishing Build Pipeline') {
            steps {
                echo 'Build pipeline finished'
            }
        }

        stage('Trigger Deploy') {
            steps {
                build job: 'Roberta-Deploy', wait: false, parameters: [
                    string(name: 'ROBERTA_IMAGE_URL', value: "deepanshurawat6/roberta-bot:latest")
                ]
            }
        }
    }
}