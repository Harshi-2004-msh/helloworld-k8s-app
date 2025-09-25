pipeline {
    agent any

    environment {
        IMAGE_NAME = "harshi39/harshitha-site"
        IMAGE_TAG = "v2"
        K8S_NAMESPACE = "training"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/Harshi-2004-msh/helloworld-k8s-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
                }
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Image Scan (Optional)') {
            steps {
                sh "trivy image $IMAGE_NAME:$IMAGE_TAG || true"
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh "kubectl apply -f namespace.yaml"
                    sh "kubectl apply -f configmap.yaml -n $K8S_NAMESPACE"
                    sh "kubectl apply -f secret.yaml -n $K8S_NAMESPACE"
                    sh "kubectl apply -f pvc.yaml -n $K8S_NAMESPACE"
                    sh "kubectl apply -f deployment.yaml -n $K8S_NAMESPACE"
                    sh "kubectl apply -f service.yaml -n $K8S_NAMESPACE"
                }
            }
        }
    }
}
