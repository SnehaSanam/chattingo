pipeline {
    agent any

    environment {
 // Docker Images
        FRONTEND_IMAGE = "chattingo-frontend"
        BACKEND_IMAGE = "chattingo-backend"
        DOCKER_TAG = "latest"

    stages {
        stage('Git Clone') {
            steps {
                sh "git branch: 'main', url: 'https://github.com/SnehaSanam/chattingo.git'"
            }
        }
        stage('Build Docker Images') {
            steps {
                sh """
                    docker build -t ${FRONTEND_IMAGE}:${DOCKER_TAG} -f frontend/Dockerfile ./frontend
                    docker build -t ${BACKEND_IMAGE}:${DOCKER_TAG} -f backend/Dockerfile ./backend
                    docker images
                """
            }
        }
        stage('Push to Docker Hub') {
            steps {
                 withCredentials([usernamePassword(
                credentialsId: "dockerhub_creds",
                usernameVariable: "DOCKER_HUB_USER",
                passwordVariable: "DOCKER_HUB_PASS"
                 )]) {
                sh """
                 echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin
                docker tag ${FRONTEND_IMAGE}:${DOCKER_TAG} $DOCKER_HUB_USER/${FRONTEND_IMAGE}:${DOCKER_TAG}
                docker tag ${BACKEND_IMAGE}:${DOCKER_TAG} $DOCKER_HUB_USER/${BACKEND_IMAGE}:${DOCKER_TAG}
                docker push $DOCKER_HUB_USER/${FRONTEND_IMAGE}:${DOCKER_TAG}
                docker push $DOCKER_HUB_USER/${BACKEND_IMAGE}:${DOCKER_TAG}
                """
            }
        }    
    }  
        stage('Deploy with Docker Compose') {
            steps {
                script {
                    withCredentials([
                        string(credentialsId: 'SPRING_DATASOURCE_PASSWORD', variable: 'J_SPRING_DB_PWD'),
                        string(credentialsId: 'jwt-secret',                variable: 'J_JWT_SECRET'),
                        string(credentialsId: 'mysql-root-password',       variable: 'J_MYSQL_ROOT_PWD'),
                        string(credentialsId: 'SPRING_PROFILES_ACTIVE',     variable: 'J_PROFILES'),
                        string(credentialsId: 'SERVER_PORT',                variable: 'J_SERVER_PORT'),
                        string(credentialsId: 'WEBSOCKET_ENDPOINT',         variable: 'J_WS_EP')
                    ]) {
                        sh '''
                            # Export runtime secrets for docker compose
                            export JWT_SECRET="${J_JWT_SECRET}"
                            export SPRING_DATASOURCE_PASSWORD="${J_SPRING_DB_PWD}"
                            export SPRING_DATASOURCE_USERNAME="root"
                            export SPRING_DATASOURCE_URL="jdbc:mysql://mysql:3306/chattingo_db?createDatabaseIfNotExist=true
                            export SPRING_PROFILES_ACTIVE="${J_PROFILES}"
                            export SERVER_PORT="${J_SERVER_PORT}"
                            export WEBSOCKET_ENDPOINT="${J_WS_EP}"
                            export MYSQL_ROOT_PASSWORD="${J_MYSQL_ROOT_PWD}"
                            export MYSQL_DATABASE="chattingo_db"

                            export CORS_ALLOWED_ORIGINS="http://72.60.111.26:3000"
                            export CORS_ALLOWED_METHODS="GET,POST,PUT,DELETE,OPTIONS,PATCH"
                            export CORS_ALLOWED_HEADERS="*"

                            # Stop all containers
                            docker compose down || true
                            docker compose pull || true
                            docker compose up -d                        '''
                        }
                    }
                }
            }               
        }
    }
 }         