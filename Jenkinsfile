pipeline {
    agent any

    environment {
        PATH = "/usr/local/bin:/usr/bin:${env.PATH}"
    }

    stages {
        stage('Stopping services') {
            steps {

                sh 'docker-compose down || true'
            }
        }

        stage('Pulling update') {
            steps {
                checkout scm
            }
        }

        stage('Building new images') {
            steps {

                sh 'docker-compose build --no-cache'
            }
        }

        stage('Deploying containers') {
            steps {
                sh 'docker-compose up -d'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker image prune -f'
            }
        }
    }

    post {
        success {
            echo '✅ Despliegue exitoso!'
        }
        failure {
            echo '❌ Error en el despliegue. Revisa los logs.'
        }
    }
}