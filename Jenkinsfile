pipeline {
    agent any

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
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    environment {
                        npm_config_cache = 'npm-cache'
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }

                }
            }
        }
        */

        stage('Deploy') {
            agent {
                docker {
                    image 'node:current-alpine'
                    args "-v /etc/passwd:/etc/passwd"
                    reuseNode true
                }
            }
            environment {
                npm_config_cache = 'npm-cache'
            }
            steps {
                sh '''
                    npm install netlify-cli
                    ls -la
                    ls -la node_modules/
                    ls -la node_modules/.bin/
                    ls -la /var/lib/jenkins/
                    ls -la /var/lib/jenkins/.config/
                    ls -la /var/lib/jenkins/netlify-cli/bin/run.js
                    cat /var/lib/jenkins/netlify-cli/bin/run.js
                    netlify --version
                '''
            }
        }
    }
}