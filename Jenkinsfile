pipeline {
    agent { label 'jenkins-agent' }

    tools {
        jdk 'Temurin JDK 17'  // Asegúrate de que coincida con el nombre en Global Tool Configuration
        maven 'Maven3'
    }

    environment {
        JAVA_HOME = '/usr/lib/jvm/temurin-17-jdk-amd64'
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
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
                sh "java -version"
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
