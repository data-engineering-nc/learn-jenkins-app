pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'ca-central-1'
        AWS_ECS_CLUSTER = 'LearnJenkinsApp-Cluster-Prod'
        AWS_ECS_SERVICE_PROD = 'LearnJenkinsApp-Service-Prod'
        AWS_ECS_TD_PROD = 'LearnJenkinsApp-TaskDefinition-Prod'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            environment {
                npm_config_cache = 'npm-cache'
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Build docker image') {
            agent {
                docker {
                    image 'amazon/aws-cli' 
                    args "-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=''"
                    reuseNode true
                }
            }

            steps {
                sh '''
                    amazon-linux-extras install docker
                    docker build -t myjenkinsapp .
                '''
            }
        }

        stage('Deploy to AWS') {

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        yum install jq -y
                        LASTEST_TD_REVISION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json | jq '.taskDefinition.revision')
                        aws ecs update-service --cluster $AWS_ECS_CLUSTER --service $AWS_ECS_SERVICE_PROD --task-definition $AWS_ECS_TD_PROD:$LASTEST_TD_REVISION
                        aws ecs wait services-stable --cluster $AWS_ECS_CLUSTER --services $AWS_ECS_SERVICE_PROD
                    '''
                }
            }
        }



    }
}