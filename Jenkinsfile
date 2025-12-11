pipeline {
    agent any

    environment {
        DOCKER_BUILDKIT = '0'
        COMPOSE_DOCKER_CLI_BUILD = '0'
        PATH = "/usr/bin:${env.PATH}"
    }

    stages {
        stage('Stopping services') {
            steps {
                sh '''
                    docker-compose down || true
                    

                   EXISTING_CONTAINERS=$(docker ps -aq -f name=melicheck-backend -f name=melicheck-frontend -f name=melicheck-database)

                    if [ -n "$EXISTING_CONTAINERS" ]; then
                        echo "Se encontraron contenedores viejos. Eliminando..."
                        docker rm -f $EXISTING_CONTAINERS
                    else
                        echo "Limpio. No había contenedores viejos."
                    fi
                '''
            }
        }

        stage('Deleting old images') {
            steps{
                sh '''
                   DANGLING_IMAGES=$(docker images -f "dangling=true" -q)

                    if [ -n "$DANGLING_IMAGES" ]; then
                        echo "Borrando imágenes huérfanas..."
                        docker rmi -f $DANGLING_IMAGES
                    fi
                '''
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
    }

    post {
        success {
            echo '✅ Pipeline ejecutado correctamente.'
        }

        failure {
            echo '❌ Error. Revisa los logs.'
        }
    }
}
