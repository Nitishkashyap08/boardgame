pipeline {
    agent any
    tools {
        maven 'mymaven'
        jdk 'jdk17'
    }
    environment {
        DOCKER_NAME = "kashyapnitsh08"
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
        DOCKER_IMAGE = "${DOCKER_NAME}/myimage:latest"
    }
    stages {
        stage('Checkout Git') {
            steps {
                git url: 'https://github.com/Nitishkashyap08/boardgame.git', branch: 'main'
            }
        }
        stage('Verify Java') {
            steps {
                sh 'java -version'
                sh 'echo $JAVA_HOME'
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
                sh "docker build -t ${DOCKER_IMAGE} ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerCredID', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push ${DOCKER_IMAGE}"
                }
            }
        }
    }
}
