pipeline {
    agent { label 'jenkins-agente' }
    
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    //Definimos variables
    environment {
        APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "josuereydev"
            DOCKER_PASS = 'dockerhub'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
//Fin de la definicion de las variables
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
        stage("SonarQube Analysis"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token1'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
        stage ("Quality Gate"){
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token1'
                }
            }
        }
// Crear un stage para crear y empujar la imagen de docker
        stages {
        stage('Build') {
            steps {
                script {
                    // Aseguramos que el código fuente y el Dockerfile están en el repositorio
                    // Si el Dockerfile está en un subdirectorio, actualiza la ruta a 'path/to/Dockerfile'
                    // En este caso asumimos que Dockerfile está en el directorio raíz del repositorio
                    dir("${WORKSPACE}") {
                        // Construcción de la imagen Docker
                        docker_image = docker.build("${IMAGE_NAME}", "-f Dockerfile .")
                    }
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    // Iniciar sesión en DockerHub
                    docker.withRegistry('', DOCKER_PASS) {
                        // Etiquetar y subir la imagen construida
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                
                    }
                }
            }
        }

    } 
} 

