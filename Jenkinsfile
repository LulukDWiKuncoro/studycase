pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
        DOCKERHUB_REPO = '<cihuahuahua>/demo-app'
        KUBE_CONFIG = credentials('kubeconfig')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/LulukDWiKuncoro/studycase.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_REPO:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh '''
                echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                docker push $DOCKERHUB_REPO:latest
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
                    sh '''
                    export KUBECONFIG=$KUBECONFIG_FILE
                    helm upgrade --install demo-app ./helm/demo-app --namespace default --create-namespace
                    '''
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods -n default
                kubectl get svc -n default
                '''
            }
        }
    }
}
