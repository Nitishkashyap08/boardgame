pipeline {
    agent any

    tools {
        jdk 'Jdk11'
        maven 'Maven3'
    }

    stages {
        stage('Check Out') {
            steps {
                git branch: 'main', credentialsId: 'github-tokens', url: 'https://github.com/sriraju12/boardgame.git'
            }
        }

        stage('Scan File System') {
            steps {
                sh "trivy fs --format table -o trivy-fs-reports.html ."
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Sonarqube Analysis'){
          steps {
            script {
                withSonarQubeEnv(credentialsId: 'jenkins-sonar-token'){
                    sh 'mvn sonar:sonar'
                }
            }
        }
    }

    stage('Build Docker Image') {
        environment {
        DOCKER_IMAGE = "sriraju12/boardgame-app:${BUILD_NUMBER}"
    }
      steps {
        script {
              docker.build("${DOCKER_IMAGE}")
        }
      }
    }

    stage('Docker Image Scan') {
        environment {
        DOCKER_IMAGE = "sriraju12/boardgame-app:${BUILD_NUMBER}"
    }
            steps {
                sh "trivy image --format table -o trivy-image-report.html ${DOCKER_IMAGE}"
            }
        }

    stage('Push Docker Image') {
        environment {
        REGISTRY_CREDENTIALS = credentials('dockerhub-token')
    }
      steps {
        script {
              sh 'docker context use default'  
              def dockerImage = docker.image("${DOCKER_IMAGE}")
              docker.withRegistry('https://index.docker.io/v1/', "dockerhub-token") {
                 dockerImage.push()
            }
        }
      }
    }
  }
}
