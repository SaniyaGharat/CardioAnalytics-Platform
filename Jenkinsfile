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

                    // Safe Windows login: Write to a temporary file and pipe it in
                    bat '''
                        echo %PASS%> docker_pass.txt
                        type docker_pass.txt | docker login -u %USER% --password-stdin
                        del docker_pass.txt
                    '''
                    bat 'docker push %IMAGE_NAME%:%TAG%'
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