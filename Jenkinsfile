pipeline {
    agent any
    
    tools {
        nodejs 'nodejs-installer'
    }
    
    stages{
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name:'*/main']],extensions: [], userRemoteConfigs: [[url: 'https://github.com/Gedfirma/jsJen.git']]])
            }
        }
    
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }         
        }

        stage('Docker Image Build') {
            steps {
                sh 'docker build -t nodejs:{env.BUILD_NUMBER} .'
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                withAWS(credentials: 'AWS_IAM', region: 'us-east-1'){
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 170681944236.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker tag nodejs:{env.BUILD_NUMBER} 170681944236.dkr.ecr.us-east-1.amazonaws.com/nodejs-tst/nodejs:{env.BUILD_NUMBER}'
                    sh 'docker push 170681944236.dkr.ecr.us-east-1.amazonaws.com/nodejs-tst/nodejs:{env.BUILD_NUMBER}'
                }
            }
        }
    }

}   