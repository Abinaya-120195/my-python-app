pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "abinayapalraj12/my-python-app:${env.BUILD_NUMBER}"
        GITHUB_REPO = "Abinaya-120195/my-python-app"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials') // Jenkins Docker Hub credentials
        KUBECONFIG = "C:\Users\abina\.kube\config"
        ARGOCD_SERVER = "https://localhost:8081"
        KUBE_CONTEXT = "docker-desktop" // Set your Kubernetes context
        
    }
    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from the GitHub repository
                checkout scm
            }
        }
        stage('Test Docker Hub Token') {
            steps {
                script {
                    bat '''
                    echo "Testing Docker Hub Token..."
                    echo $DOCKER_CREDENTIALS
                    '''
                }
            }
        }
        stage('Docker Hub Login Test') {
            steps {
                script {
                    bat '''
                    echo "Logging in to Docker Hub..."
                    echo   docker login --username abinayapalraj12 --password Information@12
                    '''
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Log in to Docker
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        // Build and push the Docker image
                        bat "docker build -t ${DOCKER_IMAGE} ."
                        bat "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
        stage('Switch to Kubernetes Context') {
    steps {
        script {
            // Switch to the specified Kubernetes context
            def kubeContext = "${KUBE_CONTEXT}"
            echo "Switching to Kubernetes context: ${kubeContext}"
            // bat "kubectl config get-contexts"
               bat "kubectl config use-context docker-desktop"
                //bat "kubectl apply -f deployment.yaml"
            //bat "kubectl config use-context ${kubeContext}"
        }
    }
}
      stage('Deploy with Argo CD') {
            steps {
                script {
                    // Use kubectl command to sync with Argo CD
                    echo "Current KUBE_CONTEXT: ${KUBE_CONTEXT}"
                     bat "kubectl config get-contexts"
               bat "kubectl config use-context docker-desktop"
                bat "kubectl apply -f deployment.yaml"
                   // bat "kubectl config use-context ${KUBE_CONTEXT}"
                       bat "argocd app sync my-python-app"
                   
                }
            }
        }
    }
    post {
        //always {
            // Archive logs if needed
           // archiveArtifacts artifacts: '**/*.log', allowEmptyArchive: true
       // }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

