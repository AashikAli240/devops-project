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
        IMAGE_NAME     = "devops-project"
        TAG            = "v1"
        DOCKERHUB_REPO = "aashikali240/devops-demo"
        CONTAINER_NAME = "devops-app"
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

                        # Push main tag
                        docker tag ${IMAGE_NAME}:${TAG} ${DOCKERHUB_REPO}:${TAG}
                        docker push ${DOCKERHUB_REPO}:${TAG}

                        # Push backup tag for rollback
                        docker tag ${IMAGE_NAME}:${TAG} ${DOCKERHUB_REPO}:backup
                        docker push ${DOCKERHUB_REPO}:backup
                    """
                }
            }
        }

        stage('Run Container') {
            when { expression { return !params.ROLLBACK } }
            steps {
                script {
                    sh "docker rm -f ${CONTAINER_NAME} || true"
                    sh "docker run -d --name ${CONTAINER_NAME} -p 8081:80 ${IMAGE_NAME}:${TAG}"
                }
            }
        }

        stage('Rollback to Backup') {
            when {
                expression { return params.ROLLBACK == true }
            }
            steps {
                script {
                    sh "docker rm -f ${CONTAINER_NAME} || true"
                    sh "docker run -d --name ${CONTAINER_NAME} -p 8081:80 ${DOCKERHUB_REPO}:backup"
                }
            }
        }
    }
}

