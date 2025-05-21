pipeline {
    agent any
    tools {
        maven 'mymaven'
        jdk 'jdk17'
    }
    environment {
        DOCKER_NAME = "kashyapnitsh08"
    }
    stages {
        stage('Checkout Git') {
            steps {
                git url: 'https://github.com/Nitishkashyap08/boardgame.git', branch: 'main'
            }
        }
        stage('Clean and Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_NAME}/myimage:latest .'
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerCredID', usernameVariable: 'DOCKER_NAME', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_NAME --password-stdin'
                    sh 'docker push $DOCKER_NAME/myimage:latest'
                }
            }
        }
    }
}
