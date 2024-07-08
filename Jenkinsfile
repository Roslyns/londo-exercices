pipeline {
    agent any

    environment {
        // Définition des variables d'environnement
        DOCKER_REGISTRY = 'your-docker-registry.com'
        DOCKER_USERNAME = 'your-docker-username'
        DOCKER_PASSWORD = 'your-docker-password'
        APPLICATION_NAME = 'your-application-name'
        ANGULAR_APP_NAME = 'angular-app'
        EXPRESS_APP_NAME = 'express-app'
        PRODUCTION.environment = 'production'
    }

    stages {
        stage('Build Angular App') {
            steps {
                // Checkout du code source
                git 'https://github.com/your-organization/your-angular-app.git'

                // Installation des dépendances
                sh 'npm install'

                // Build de l'application Angular
                sh 'ng build --prod'

                // Archivage des fichiers buildés
                archiveArtifacts 'dist/**/*'
            }
        }

        stage('Build Express App') {
            steps {
                // Checkout du code source
                git 'https://github.com/your-organization/your-express-app.git'

                // Installation des dépendances
                sh 'npm install'

                // Build de l'application Express
                sh 'npm run build'

                // Archivage des fichiers buildés
                archiveArtifacts 'build/**/*'
            }
        }

        stage('Dockerize Applications') {
            steps {
                // Dockerisation de l'application Angular
                sh """
                    docker build -t ${DOCKER_REGISTRY}/${ANGULAR_APP_NAME} .
                    docker tag ${DOCKER_REGISTRY}/${ANGULAR_APP_NAME} ${DOCKER_REGISTRY}/${ANGULAR_APP_NAME}:${PRODUCTION.environment}
                """

                // Dockerisation de l'application Express
                sh """
                    docker build -t ${DOCKER_REGISTRY}/${EXPRESS_APP_NAME} .
                    docker tag ${DOCKER_REGISTRY}/${EXPRESS_APP_NAME} ${DOCKER_REGISTRY}/${EXPRESS_APP_NAME}:${PRODUCTION.environment}
                """
            }
        }

        stage('Push to Docker Registry') {
            steps {
                // Authentification avec le registry Docker
                withCredentials([usernamePassword(credentialsId: 'your-docker-credentials-id', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    sh """
                        echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin ${DOCKER_REGISTRY}
                    """
                }

                // Push des images Docker vers le registry
                sh """
                    docker push ${DOCKER_REGISTRY}/${ANGULAR_APP_NAME}:${PRODUCTION.environment}
                    docker push ${DOCKER_REGISTRY}/${EXPRESS_APP_NAME}:${PRODUCTION.environment}
                """
            }
        }

        stage('Deploy to Production') {
            steps {
                // Définition des variables d'environnement pour la production
                environment {
                    PRODUCTION_SERVER = 'your-production-server.com'
                }

                // Déploiement des applications vers la production
                sh """
                    ssh ${PRODUCTION_SERVER} "docker pull ${DOCKER_REGISTRY}/${ANGULAR_APP_NAME}:${PRODUCTION.environment}"
                    ssh ${PRODUCTION_SERVER} "docker pull ${DOCKER_REGISTRY}/${EXPRESS_APP_NAME}:${PRODUCTION.environment}"
                    ssh ${PRODUCTION_SERVER} "docker-compose up -d"
                """
            }
        }
    }
}