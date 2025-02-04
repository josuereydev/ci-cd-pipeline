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
                sh "ls -la"  // Verificar si el código se clonó correctamente
            }
        }

        stage("Build Application") {
            steps {
                sh """
                if [ -f pom.xml ]; then
                    mvn clean package
                elif [ -d app ] && [ -f app/pom.xml ]; then
                    cd app && mvn clean package
                else
                    echo "No se encontró el pom.xml en el directorio esperado" && exit 1
                fi
                """
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }
    }

    post {
        failure {
            echo "El build ha fallado. Revisa los logs."
        }
        success {
            echo "Build exitoso 🎉"
        }
    }
}


