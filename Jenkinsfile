pipeline {
    agent any

    tools {
        nodejs 'node'
    }

    environment {
        IMAGE_VERSION = 'v1.0'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:${IMAGE_VERSION} .'
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:${IMAGE_VERSION} .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        sh '''
                            docker rm -f nodemain-app || true
                            docker run -d \
                                --name nodemain-app \
                                -p 3000:3000 \
                                nodemain:${IMAGE_VERSION}
                        '''
                    } else if (env.BRANCH_NAME == 'dev') {
                        sh '''
                            docker rm -f nodedev-app || true
                            docker run -d \
                                --name nodedev-app \
                                -p 3001:3000 \
                                nodedev:${IMAGE_VERSION}
                        '''
                    }
                }
            }
        }
    }
}