pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'chethanraj13/jenkins'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Install dependencies') {
            steps {
               sh 'pip install --no-cache-dir --prefix=/tmp -r requirements.txt'
            }
        }

        stage('Run tests') {
            steps {
                sh 'python -m unittest discover || echo "No tests yet"'
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'docker build -t $DOCKER_HUB_REPO:$IMAGE_TAG .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_HUB_REPO:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy via Argo CD') {
            steps {
                sh '''
                    echo "Assuming Argo CD is watching your GitOps repo that uses this image..."
                    echo "Update image tag in your Kubernetes manifest and push to GitOps repo"
                '''
            }
        }
    }
}
