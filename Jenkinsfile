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
        
        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/josuereydev/ci-cd-pipeline.git'
            }
        }

        // Construir la aplicación        
        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        // Testear la aplicación       
        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        // SonarQube Analysis
        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube') { 
                        sh "mvn sonar:sonar"
                    }
                }   
            }
        }
    }

    // Agregar bloque post para manejar errores y limpiar
    post {
        always {
            echo 'Pipeline finalizado.'
        }
        failure {
            echo 'La ejecución falló. Revisar logs.'
        }
    }
}