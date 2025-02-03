pipeline {
    agent { label 'jenkins-agent' }

    tools {
        jdk 'Java17'  // Asegúrate de que 'Java17' está correctamente configurado en Global Tool Configuration
        maven 'Maven3'
    }

    environment {
        JAVA_HOME = '/usr/lib/jvm/temurin-17-jdk-amd64'  // Ruta correcta dentro del contenedor
        PATH = "${JAVA_HOME}/bin:${env.PATH}"  // Añade Java al PATH
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

        stage("Verify Java Version") {
            steps {
                sh "echo 'Using JAVA_HOME=${JAVA_HOME}'"
                sh "java -version"  // Verifica que Jenkins usa Java 17
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }
    }

    post {
        success {
            echo "✅ Build completado con éxito"
        }
        failure {
            echo "❌ Hubo un error en la ejecución del pipeline"
        }
    }
}
