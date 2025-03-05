pipeline {
    agent { label 'jenkins-agente' }
    
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }
//Viriables Docker
        environment {
            APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "josuereydev"
            DOCKER_PASS = 'dockerhub'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	              
        }
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/josuereydev/ci-cd-pipeline.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }
        stage ("SonarQube Analysis"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'tokensonarqube-2') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage("Quality Gate"){
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'tokensonarqube-2'
                }
            }
        }
//Construir la imagen de Docker
        stage("Build & Push Docker Image"){
            steps {
                script {docker.withRegistry('',DOCKER_PASS) {
                    docker_image = docker.build "${IMAGE_NAME}"
                }
                docker.withRegistry('',DOCKER_PASS) {
                   docker_image.push("${IMAGE_TAG}")
                   docker_image.push('latest')
                }
                }
            }
        }
    }
  }