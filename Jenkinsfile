
pipeline {
    agent any

    environment {
        IMAGE = "irannachawan31/flask-app:latest"
        CREDS_ID = "Docker_cred"
    }

    stages {
        stage('Clone Code') {
            steps {
                git credentialsId: 'irannachawan', url: 'https://github.com/irannachawan/App_Deploy_K8s.git', branch: 'main'
            }
        }

        stage('Verify Docker Access') {
            steps {
                sh '''
                    echo "🔍 Checking Docker access..."
                    echo "Running as: $(whoami)"
                    echo "Groups: $(groups)"
                    docker version
                    docker ps
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: CREDS_ID, usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh '''
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push $IMAGE
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}
