pipeline {
    agent any

    environment {
        IMAGE_NAME = "saniyagharat25/cardioanalytics"
        TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Suryanshsaraf/DEVOPS-APDD.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%TAG% .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')]) {

                    bat 'echo %PASS% | docker login -u %USER% --password-stdin'
                    bat 'docker tag %IMAGE_NAME%:%TAG% %IMAGE_NAME%:latest'
                    bat 'docker push %IMAGE_NAME%:%TAG%'
                    bat 'docker push %IMAGE_NAME%:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG')]) {
                    bat 'kubectl apply -f k8s/ --kubeconfig="%KUBECONFIG%"'
                    bat 'kubectl rollout restart deployment/ml-prediction-api -n ml-production --kubeconfig="%KUBECONFIG%"'
                }
            }
        }
    }

    post {
        success {
            echo 'Build Successful'
        }
        failure {
            echo 'Build Failed'
        }
    }
}