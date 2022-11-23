@Library('github.com/releaseworks/jenkinslib') _

pipeline {
    agent any
    environment {
        registry = "246697379410.dkr.ecr.us-east-1.amazonaws.com/project"
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/surajvenkatesh/Jenkins_task.git']]])
            }
        }

    // Building Docker images


    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
        steps{
            script {
                sh 'docker build -t 246697379410.dkr.ecr.us-east-1.amazonaws.com/project . '
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 246697379410.dkr.ecr.us-east-1.amazonaws.com '
                sh 'docker push 246697379410.dkr.ecr.us-east-1.amazonaws.com/project'
            }
        }
    }

    
    stage('Docker Run') {
     steps{
         script {
             sshagent(credentials : ["970f927e-13eb-430e-800b-384c851f3978"]){

                sh 'ssh -t -t ubuntu@10.0.2.52 -o StrictHostKeyChecking=no "docker login -u AWS -p $(aws ecr get-login-password --region us-east-1) 246697379410.dkr.ecr.us-east-1.amazonaws.com && docker run -d -p 8080:8081 --rm --name nodeapp 246697379410.dkr.ecr.us-east-1.amazonaws.com/project:latest"'

             }
                
                
            }
      }
    }
    }
}
