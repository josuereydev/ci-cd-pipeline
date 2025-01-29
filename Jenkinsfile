pipeline {
    agent { label 'Jenkins-agent' }

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
