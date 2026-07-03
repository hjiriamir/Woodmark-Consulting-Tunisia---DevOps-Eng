pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "my-app"
        DOCKER_TAG = "latest"
        KUBE_NAMESPACE = "default"
        DEPLOYMENT_NAME = "my-app"
    }
    
    stages {
        stage('Checkout') {
            steps {
                echo '📦 Checking out code from GitHub...'
                // This will be automated via GitHub webhook
            }
        }
        
        stage('Build Application') {
            steps {
                echo '🔨 Building application...'
                sh '''
                    echo "Building application..."
                    # For Node.js apps
                    npm install
                    npm test
                '''
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh '''
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    docker images | grep ${DOCKER_IMAGE}
                '''
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                echo '☸️ Deploying to Kubernetes...'
                sh '''
                    # Create deployment
                    kubectl create deployment ${DEPLOYMENT_NAME} --image=${DOCKER_IMAGE}:${DOCKER_TAG} --dry-run=client -o yaml > deployment.yaml
                    
                    # Apply to Kubernetes
                    kubectl apply -f deployment.yaml
                    
                    # Expose service
                    kubectl expose deployment ${DEPLOYMENT_NAME} --port=80 --target-port=3000 --type=NodePort --dry-run=client -o yaml > service.yaml
                    kubectl apply -f service.yaml
                    
                    # Check status
                    kubectl get pods
                    kubectl get services
                '''
            }
        }
        
        stage('Verify Deployment') {
            steps {
                echo '✅ Verifying deployment...'
                sh '''
                    kubectl get pods
                    kubectl get services
                    echo "Application deployed successfully!"
                '''
            }
        }
    }
    
    post {
        success {
            echo '🎉 Pipeline completed successfully!'
            echo '🌐 Application is available at:'
            sh 'minikube service ${DEPLOYMENT_NAME} --url'
        }
        failure {
            echo '❌ Pipeline failed!'
            echo '📋 Check the logs above for errors.'
        }
    }
}