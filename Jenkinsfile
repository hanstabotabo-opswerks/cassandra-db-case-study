pipeline {
    agent {
        kubernetes {
            inheritFrom 'k8s-agent'
            defaultContainer 'docker'
        }
    }
    environment {
        DOCKERHUB_USER = 'romeofrancobarro'
        IMAGE_TAG = 'prod'  // Change to a specific version if needed, like 'v1.0'
    }
    stages {
        stage('Prepare Kubeconfig') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-secret', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                        mkdir -p ~/.kube
                        cp $KUBECONFIG_FILE ~/.kube/config
                        export KUBECONFIG=~/.kube/config
                    '''
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'RomeoDocker', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }
        stage('Build Docker Images') {
            steps {
                script {
                    sh 'docker compose build'
                }
            }
        }
        stage('Tag and Push Docker Images') {
            steps {
                script {
                    def services = ['frontend', 'backend', 'cassandra']
                    for (service in services) {
                        def imageName = "${DOCKERHUB_USER}/${service}:${IMAGE_TAG}"
                        sh "docker tag prod-${service}:latest ${imageName}"
                        sh "docker push ${imageName}"
                    }
                }
            }
        }
        stage('Unit Tests') {
            steps {
                script {
                    sh '''
                        docker run --rm -w /app ${DOCKERHUB_USER}/frontend:${IMAGE_TAG} /bin/sh -c "
                        apk add --no-cache nodejs npm && npm test -- --watchAll=false
                        "
                    '''
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deploy_prod.yaml'
                }
            }
        }
    }
    post {
        always {
            sh 'docker system prune -f'
        }
    }
}
