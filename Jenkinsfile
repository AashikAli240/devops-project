pipeline {
    agent any

    triggers {
        pollSCM('* * * * *')   // Auto build every 1 minute
    }

    parameters {
        booleanParam(
            name: 'ROLLBACK',
            defaultValue: false,
            description: 'Run rollback using backup image'
        )
    }

    environment {
        IMAGE_NAME      = "devops-project"
        TAG             = "v1"
        DOCKERHUB_REPO  = "aashikali240/devops-demo"

        BLUE_NAME       = "devops-app-blue"
        GREEN_NAME      = "devops-app-green"

        BLUE_PORT       = "8081"
        GREEN_PORT      = "8082"

        NGINX_SITE      = "/etc/nginx/sites-available/default"
    }

    stages {

        stage('Checkout SCM') {
            when { expression { return !params.ROLLBACK } }
            steps {
                git branch: 'main',
                    url: 'https://github.com/AashikAli240/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${TAG} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            when { expression { return !params.ROLLBACK } }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        echo "$PASS" | docker login -u "$USER" --password-stdin

                        # main tag
                        docker tag ${IMAGE_NAME}:${TAG} ${DOCKERHUB_REPO}:${TAG}
                        docker push ${DOCKERHUB_REPO}:${TAG}

                        # backup tag for rollback
                        docker tag ${IMAGE_NAME}:${TAG} ${DOCKERHUB_REPO}:backup
                        docker push ${DOCKERHUB_REPO}:backup
                    """
                }
            }
        }

        stage('Deploy GREEN') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh "docker rm -f ${GREEN_NAME} || true"
                    sh "docker run -d --name ${GREEN_NAME} -p ${GREEN_PORT}:80 ${IMAGE_NAME}:${TAG}"
                }
            }
        }

        stage('Health Check GREEN') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh """
                        for i in {1..10}; do
                            echo "Checking GREEN on port ${GREEN_PORT} (attempt \$i)..."
                            if curl -fsS http://localhost:${GREEN_PORT} > /dev/null; then
                                echo "GREEN is healthy"
                                exit 0
                            fi
                            sleep 2
                        done
                        echo "GREEN failed health check"
                        exit 1
                    """
                }
            }
        }

        stage('Switch Traffic to GREEN') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh """
                        sudo sed -i 's/${BLUE_PORT}/${GREEN_PORT}/g' ${NGINX_SITE}
                        sudo nginx -t
                        sudo systemctl reload nginx
                    """
                }
            }
        }

        stage('Stop BLUE') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh "docker rm -f ${BLUE_NAME} || true"
                }
            }
        }

        stage('Rollback to Backup') {
            when { expression { return params.ROLLBACK == true } }
            steps {
                script {
                    sh "docker rm -f ${BLUE_NAME} ${GREEN_NAME} || true"
                    sh "docker run -d --name ${BLUE_NAME} -p ${BLUE_PORT}:80 ${DOCKERHUB_REPO}:backup"

                    // Nginx ko wapas BLUE par point karo
                    sh """
                        sudo sed -i 's/${GREEN_PORT}/${BLUE_PORT}/g' ${NGINX_SITE} || true
                        sudo nginx -t || true
                        sudo systemctl reload nginx || true
                    """
                }
            }
        }
    }
}

