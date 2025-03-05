pipeline {
    agent { label 'jenkins-agente' }
    
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "josuereydev"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
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

        stage ("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'tokensonarqube-2') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    timeout(time: 5, unit: 'MINUTES') {  // Evita bloqueos largos
                        def qualityGate = waitForQualityGate()
                        if (qualityGate.status != 'OK') {
                            error "Quality Gate failed: ${qualityGate.status}"
                        }
                    }
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    def docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}") // Se declara antes de usarla
                    
                    docker.withRegistry('', 'dockerhub') { // Se usa credentialsId correcto
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
    }
}