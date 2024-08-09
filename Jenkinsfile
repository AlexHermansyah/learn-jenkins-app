pipeline {
    agent any
    environment {
        NETLIFY_SITE_ID = 'b3dc5ff7-ed0a-4f29-8d38-01f491fbf2d3'
         NETLIFY_AUTH_TOKEN = credentials('netlify-token') 
        // REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages{
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
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
                stage('Unit Tests') {
                 // parallel {
                //stage('Unit test') {
                                    agent {
                                        docker {
                                            image 'node:18-alpine'
                                            reuseNode true
                                        }
                                    }

                                    steps {
                                        sh '''
                                            #tests -f build/index.html
                                            npm tests
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
                                    steps {
                                        sh '''
                                            npm install serve
                                            node_modules/.bin/serve -s build &
                                            sleep 10
                                            npx playwright test --reporter=html
                                        '''
                                    }
                        //         }
                        //     }
                        // }
                    // }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
            // steps {
            //     bat '''
            //         node --version
            //         netlify --version
            //         echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
            //         netlify status
            //         netlify deploy --dir=build --prod
            //         npx playwright test  --reporter=html
            //     '''
            // }
            // post {
            //     always {
            //         publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod E2E', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }