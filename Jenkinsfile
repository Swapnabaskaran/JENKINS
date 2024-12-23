pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'sample-app'
        DOCKER_REGISTRY = 'prajiith/sample-app'
        CLOUD_BUCKET = 's3://PRAJIITH'
    }

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE pytest tests/'
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: 'git-credentials']) {
                    sh 'docker tag $DOCKER_IMAGE $DOCKER_REGISTRY:latest'
                    sh 'docker push $DOCKER_REGISTRY:latest'
                }
            }
        }

        stage('Simulate Cloud Deployment') {
            steps {
                echo 'Simulating cloud deployment'
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-credentials']]) {
                    sh 'aws s3 cp deployment.zip $CLOUD_BUCKET'
                }
            }
        }
    }
}