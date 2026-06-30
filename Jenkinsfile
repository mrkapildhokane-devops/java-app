pipeline {
    agent any

    tools {
        maven 'maven'
    }

    environment {
        DOCKER_IMAGE = "kapildhokane/java-app"
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main',
                git 'https://github.com/mrkapildhokane-devops/java-app.git'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:${BUILD_NUMBER} .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                usernamePassword(
                credentialsId: 'dockerhub',
                usernameVariable: 'USER',
                passwordVariable: 'PASS'
                )
                ]) {

                    sh '''
                    docker login -u $USER -p $PASS
                    docker push $DOCKER_IMAGE:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                kubectl set image deployment/java-app \
                java-app=$DOCKER_IMAGE:${BUILD_NUMBER}
                '''
            }
        }
    }
}
