pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials') 
        KUBECONFIG = credentials('kubeconfig-credentials')
    }

    stages {
        stage('Docker Login') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_HUB_CREDENTIALS) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                    }
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                script {
                    def imageTag = 'roor007/assessment-krn-node' 
                    def checksumTag = sh(returnStdout: true, script: "sha256sum Dockerfile | awk '{print \$1}'").trim()
					cd sample-service
                    sh "docker build -t $imageTag:latest -t $imageTag:$checksumTag ."
                    sh "docker push $imageTag:latest"
                    sh "docker push $imageTag:$checksumTag"
                }
            }
        }

        stage('Kubernetes Deployment') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'kubeconfig-credential-id', variable: 'KUBECONFIG')]) {
                        sh "kubectl apply -f assessment-krn-k8s/deployments/assessment-krn-deployment.yaml"
                        sh "kubectl rollout status deployment/assessment-deployment -n assessment-krn"
                    }
                }
            }
        }
    }
}
