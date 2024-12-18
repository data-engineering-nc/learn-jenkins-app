pipeline {
    agent any

    environment {
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = 'ca-central-1'
    }

    stages {
        /*
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
        */

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli' 
                    args "--entrypoint=''"
                    reuseNode true
                }
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws ecs register-task-definition --cli-input-json aws/task-definition.json
                    '''
                }
            }
        }



    }
}