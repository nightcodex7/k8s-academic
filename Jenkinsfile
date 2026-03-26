pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nightcodex/k8s-academic"
        IMAGE_TAG = "${env.BUILD_NUMBER}"
    }

    stages {
        
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             echo "Building Docker Image..."
        //             sh "docker build -t ${DOCKER_IMAGE}:latest -t ${DOCKER_IMAGE}:${IMAGE_TAG} ."
        //         }
        //     }
        // }

        // stage('Push to Docker Hub') {
        //     steps {
        //         script {
        //             withCredentials([usernamePassword(credentialsId: 'ncxDocker', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
        //                 sh "echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin"
        //                 sh "docker push ${DOCKER_IMAGE}:latest"
        //                 sh "docker push ${DOCKER_IMAGE}:${IMAGE_TAG}"
        //             }
        //         }
        //     }
        // }

        stage('Deploy to Minikube') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'minikube-kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                        sh """
                        export KUBECONFIG=\$KUBECONFIG_FILE
                        kubectl config current-context
                        kubectl cluster-info
                        kubectl apply -f deployment.yml
                        kubectl apply -f service.yml
                        kubectl apply -f ingress.yml
                        kubectl apply -f hpa.yml
                        kubectl set image deployment/k8s-academic-deployment k8s-academic-container=${DOCKER_IMAGE}:4
                        kubectl get pod -w
                        kubectl rollout status deployment/k8s-academic-deployment
                        """
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo "Pipeline executed successfully! Deployed to k8s.nightcode.co.in"
        }
        failure {
            echo "Pipeline failed...!"
        }
    }
}