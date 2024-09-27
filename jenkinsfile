pipeline {
    agent any
    
    environment {
        KUBE_NAMESPACE = 'webapp'
        DOCKER_DIR = 'Docker-images' // Path to Dockerfiles in your repository
    }

    stages {
        stage('Prepare Minikube Docker Environment') {
            steps {
                script {
                    // Configure Docker to use Minikube's Docker daemon
                    sh 'eval $(minikube docker-env)'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    // Assuming multiple services have separate Dockerfiles
                    // Build each Dockerfile inside Docker-images directory
                    sh '''
                    
                    docker build -t go-lang:latest $DOCKER_DIR/Go_Backend/
                    docker build -t proxy-local $DOCKER_DIR/proxy-local/
                   
                    '''
                }
            }
        }

        stage('Create Kubernetes Namespace') {
            steps {
                script {
                    // Create the 'webapp' namespace if it doesn't exist
                    sh '''
                    kubectl get ns $KUBE_NAMESPACE || kubectl create namespace $KUBE_NAMESPACE
                    '''
                }
            }
        }

        stage('Deploy Kubernetes Resources') {
            steps {
                script {
                    // Apply all the resources (YAML manifests) to the 'webapp' namespace
                    sh '''
                    kubectl apply -n $KUBE_NAMESPACE -f Secret/
                    kubectl apply -n $KUBE_NAMESPACE -f StatfullSet/
                    kubectl apply -n $KUBE_NAMESPACE -f Deployments/
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}