pipeline {
    agent any

    environment {
        SONAR_HOST_URL = "http://13.216.157.157:9000"
        SONAR_TOKEN = "squ_3a54c59f2cd30bc17c4e53396a6e6f9f99f0423a"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/shubhamsharmacs1/DevOps-Project-Swiggy.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install || true'
            }
        }

        stage('SonarQube Scan') {
            steps {
                sh '''
                docker run --rm \
                  -e SONAR_HOST_URL=$SONAR_HOST_URL \
                  -e SONAR_LOGIN=$SONAR_TOKEN \
                  -v $(pwd):/usr/src \
                  sonarsource/sonar-scanner-cli \
                  -Dsonar.projectKey=Swiggy \
                  -Dsonar.projectName=Swiggy \
                  -Dsonar.sources=.
                '''
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh 'docker run --rm -v $(pwd):/app aquasec/trivy fs /app > trivyfs.txt'
            }
        }

        stage('Docker Build & Push') {
            steps {
                sh 'docker build -t swiggy .'
                sh 'docker tag swiggy shubhamsharma01/swiggy:latest'
                sh 'docker push shubhamsharma01/swiggy:latest'
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'docker run --rm aquasec/trivy image shubhamsharma01/swiggy:latest > trivy.txt'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f swiggy || true
                docker run -d -p 3002:3000 --name swiggy shubhamsharma01/swiggy:latest
                '''
            }
        }
    }
}
