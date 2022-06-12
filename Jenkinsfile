pipeline {
    agent any
    environment {
        CREDS_FOR_AWS = credentials("ci-cd-tutorial-sample-app-public-aws-creds")
    }
    options { disableConcurrentBuilds() }
    stages {
        stage('Build') {
            steps {
                sh 'echo "My first pipeline"'
                sh '''
                    echo "By the way, I can do more stuff in here"
                    ls -lah
                '''
            }
        }
        stage('Test') {
            steps {
                sh 'echo "Testing!"'
                sh 'aws --version'
            }
        }
        stage("PR's"){
            when{
                branch "PR-*"
            }
            steps{
                echo 'this only runs for PRs'
            }
        }
        stage('Staging') {
            when{
                branch "staging"
            }
            steps {
                sh 'echo "Staging!"'
                sh 'aws cloudformation create-stack \
                    --stack-name cicd-example-stack-name-staging \
                    --template-body file://./aws-cf-ecs-template.yaml \
                    --capabilities CAPABILITY_NAMED_IAM \
                    --region eu-central-1 \
                    --parameters ParameterKey=SubnetID,ParameterValue=subnet-d76dc19b ParameterKey=ImageName,ParameterValue=025628008566.dkr.ecr.eu-central-1.amazonaws.com/flask-app-image:latest'
            }
        }
        stage('Production') {
            when{
                branch "main"
            }
            steps {
                sh 'echo "Production on Main!"'
                sh 'aws cloudformation create-stack \
                    --stack-name cicd-example-stack-name \
                    --template-body file://./aws-cf-ecs-template.yaml \
                    --capabilities CAPABILITY_NAMED_IAM \
                    --region eu-central-1 \
                    --parameters ParameterKey=SubnetID,ParameterValue=subnet-d76dc19b ParameterKey=ImageName,ParameterValue=025628008566.dkr.ecr.eu-central-1.amazonaws.com/flask-app-image:latest'
            }
        }
    }
    post {
        always {
            echo 'I will always get executed'
        }
        success {
            echo 'I will only get executed if this success'
        }
        failure {
            echo 'I will only get executed if this fails'
        }
        unstable {
            echo 'I will only get executed if this is unstable'
        }
    }
}
