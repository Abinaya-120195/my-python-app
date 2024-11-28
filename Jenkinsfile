pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "abinayapalraj12/my-python-app:${env.BUILD_NUMBER}"
        GITHUB_REPO = "Abinaya-120195/my-python-app"
        DOCKER_CREDENTIALS = credentials('dockerhub-credentials') // Jenkins Docker Hub credentials
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
        stage('Test Credentials') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                     usernameVariable: 'DOCKER_USER', 
                                                     passwordVariable: 'DOCKER_PASS')]) {
                        sh '''
                        echo "Username: $DOCKER_USER"
                        '''
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Log in to Docker
                    docker.withRegistry('https://index.docker.io/v1/', 'DOCKER_CREDENTIALS') {
                        // Build and push the Docker image
                        sh "docker build -t ${DOCKER_IMAGE} ."
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }
       // stage('Deploy with Argo CD') {
           // steps {
               // script {
                    // Use kubectl command to sync with Argo CD
               //     sh '''
                      //  kubectl config use-context ${KUBE_CONTEXT}
                       // argocd app sync my-python-app
             //       '''
               // }
 //}
  //      }
    }
    post {
        always {
            // Archive logs if needed
            archiveArtifacts artifacts: '**/*.log', allowEmptyArchive: true
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

