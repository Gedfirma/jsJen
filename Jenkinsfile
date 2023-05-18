pipeline {
    agent any
    options {
        buildDiscarder logRotator(
            artifactDaysToKeepStr: "30",
            artifactNumToKeepStr: "100",
            daysToKeepStr: "60",
            numToKeepStr: "200"
        )
    }
    environment {
       BUILD_NUMBER = '1.1'
       dockerImage = ''
    }

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
        
        stage('Check folders and path') {
            steps{
                sh 'ls -al'
                sh 'pwd'
            }
        }
        
        stage('Docker Image Build') {
            steps {
                sh 'docker build -t nodejs:v1 .'
                //script{
                //    dockerImage = docker.build('nodejs:v1')
                //}
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                withAWS(credentials: 'AWS_IAM', region: 'us-east-1'){
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 170681944236.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker tag nodejs:${BUILD_NUMBER} 170681944236.dkr.ecr.us-east-1.amazonaws.com/nodejs-tst/nodejs:${BUILD_NUMBER}'
                    sh 'docker push 170681944236.dkr.ecr.us-east-1.amazonaws.com/nodejs-tst/nodejs:${BUILD_NUMBER}'
                }
            }
        }
    }
}   
