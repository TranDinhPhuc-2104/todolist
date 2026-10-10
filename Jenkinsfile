pipeline {
    agent any
 
    environment {
        AWS_REGION  = 'us-east-1'
        ACCOUNT_ID  = '415642395658'
        ECR_REPO    = 'todolist'
        ECS_CLUSTER = 'todolist-cluster'
        ECS_SERVICE = 'todolist-service-i1a0j840'
        IMAGE       = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}:latest"
    }
 
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }
 
        stage('Push ECR') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'aws-creds',
                        usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION \
                          | docker login --username AWS --password-stdin $ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                        docker push $IMAGE
                    '''
                }
            }
        }
 
        stage('Deploy ECS') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'aws-creds',
                        usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                        aws ecs update-service --cluster $ECS_CLUSTER --service $ECS_SERVICE \
                          --force-new-deployment --region $AWS_REGION
                        aws ecs wait services-stable --cluster $ECS_CLUSTER --services $ECS_SERVICE --region $AWS_REGION
                    '''
                }
            }
        }
    }
}