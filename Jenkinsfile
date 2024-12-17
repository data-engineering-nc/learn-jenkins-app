pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '4c996ec9-ce30-4336-9ff5-101df940b899'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {
        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli' 
                    args "--entrypoint=''"
                }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                        sh '''
                            aws --version
                            aws s3 ls
                        '''
                    }
                }
            }
        }

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

        stage('Tests') {
            parallel {
                stage('Unit tests') {
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
                            echo "Test stage"
                            test -f build/index.html
                            npm test
                        '''
                    }

                    post {
                        always {
                            junit 'jest-results/junit.xml'
                        }
                    }
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'my-playwright'
                            reuseNode true
                        }
                    }
                    environment {
                        npm_config_cache = 'npm-cache'
                    }

                    steps {
                        sh '''
                            serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }

                }
            }
        }

        stage('Deploy staging') {
            agent {
                docker {
                    image 'my-playwright'
                    args  "-v /etc/passwd:/etc/passwd -v /var/lib/jenkins/:/var/lib/jenkins/"
                    reuseNode true
                }
            }

            environment {
                npm_config_cache = 'npm-cache'
                CI_ENVIRONMENT_URL = 'STAGING_URL_TO_BE_SET'
            }

            steps {
                sh '''
                    netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --dir=build --json > deploy-output.json
                    CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)
                    npx playwright test --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Staging E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }

        }

        stage('Approval') {
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    input message: 'Do you wish to deploy to production?', ok: 'Yes, I am sure !'
                }
            }
        }
        
        stage('Deploy prod') {
            agent {
                docker {
                    image "my-playwright"
                    args  "-v /etc/passwd:/etc/passwd -v /var/lib/jenkins/:/var/lib/jenkins/"
                    reuseNode true
                }
            }

            environment {
                npm_config_cache = 'npm-cache'
                CI_ENVIRONMENT_URL = 'https://dazzling-meerkat-301f36.netlify.app'
            }

            steps {
                sh '''
                    node --version
                    netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    netlify status
                    netlify deploy --dir=build --prod
                    npx playwright test --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Production E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }

        }
        */
    }
}