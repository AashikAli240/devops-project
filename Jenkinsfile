pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "aashikali240/devops-demo"   // <-- Replace with your DockerHub username
        GIT_REPO = "https://github.com/AashikAli240/devops-project.git"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git "${GIT_REPO}"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker Image..."
                    docker build -t $DOCKER_IMAGE:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-cred',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {

                    sh '''
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    echo "Deploying to Kubernetes..."
                    kubectl apply -f k8s/
                '''
            }
        }
    }
}
