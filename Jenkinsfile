pipeline {
    agent any
 
    environment {
        AWS_REGION     = 'us-east-1'
        AWS_ACCOUNT_ID = "415642395658"          // <-- đổi thành account ID của bạn
        ECR_REPO       = 'todolist'
        ECS_CLUSTER    = 'todolist-cluster'
        ECS_SERVICE    = 'todolist-service'
        IMAGE_TAG      = "${env.BUILD_NUMBER}"
        ECR_URI        = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO}"
    }
 
    options {
        timestamps()
        disableConcurrentBuilds()
    }
 
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
 
        stage('Install & Build (verify)') {
            agent { docker { image 'node:18-alpine'; reuseNode true } }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }
 
        stage('Docker Build') {
            steps {
                sh "docker build -t ${ECR_REPO}:${IMAGE_TAG} ."
                sh "docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_URI}:${IMAGE_TAG}"
                sh "docker tag ${ECR_REPO}:${IMAGE_TAG} ${ECR_URI}:latest"
            }
        }
 
        stage('Push to ECR') {
            steps {
                // Jenkins chạy trên EC2 có IAM Role -> không cần access key.
                // Nếu dùng key: bọc bằng withCredentials([[$class:'AmazonWebServicesCredentialsBinding', credentialsId:'aws-creds']])
                sh """
                    aws ecr get-login-password --region ${AWS_REGION} \
                      | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                    docker push ${ECR_URI}:${IMAGE_TAG}
                    docker push ${ECR_URI}:latest
                """
            }
        }
 
        stage('Deploy to ECS') {
            steps {
                sh """
                    sed -e 's|__IMAGE__|${ECR_URI}:${IMAGE_TAG}|' \
                        -e 's|__ACCOUNT_ID__|${AWS_ACCOUNT_ID}|' \
                        -e 's|__REGION__|${AWS_REGION}|' \
                        ecs/task-definition.json > ecs/task-definition.rendered.json
 
                    NEW_TD=\$(aws ecs register-task-definition \
                        --cli-input-json file://ecs/task-definition.rendered.json \
                        --region ${AWS_REGION} \
                        --query 'taskDefinition.taskDefinitionArn' --output text)
                    echo "Registered: \$NEW_TD"
 
                    aws ecs update-service \
                        --cluster ${ECS_CLUSTER} --service ${ECS_SERVICE} \
                        --task-definition \$NEW_TD --region ${AWS_REGION}
 
                    aws ecs wait services-stable \
                        --cluster ${ECS_CLUSTER} --services ${ECS_SERVICE} --region ${AWS_REGION}
                """
            }
        }
    }
 
    post {
        success { echo "Deployed ${ECR_URI}:${IMAGE_TAG} to ${ECS_SERVICE}" }
        failure { echo 'Pipeline failed - xem Console Output' }
        always  { sh "docker rmi ${ECR_URI}:${IMAGE_TAG} ${ECR_URI}:latest ${ECR_REPO}:${IMAGE_TAG} || true" }
    }
}
 