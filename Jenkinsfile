pipeline {
    agent any

    // environment {
    //     NETLIFY_SITE_ID = 'YOUR NETLIFY SITE ID'
    //     NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    //     REACT_APP_VERSION = "1.0.$BUILD_ID"
    // }

    stages {

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

        stage('Test') {
            // parallel {
            //     stage('Unit test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
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

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test
                        '''
                    }
                }
            }
                    post {
                        always {
                            junit 'jest-result/junit.xml'
                        }
                    }
                }

    //     stage('Deploy staging') {
    //         agent {
    //             docker {
    //                 image 'my-playwright'
    //                 reuseNode true
    //             }
    //         }

    //         environment {
    //             CI_ENVIRONMENT_URL = 'STAGING_URL_TO_BE_SET'
    //         }

    //         steps {
    //             sh '''
    //                 netlify --version
    //                 echo "Deploying to staging. Site ID: $NETLIFY_SITE_ID"
    //                 netlify status
    //                 netlify deploy --dir=build --json > deploy-output.json
    //                 CI_ENVIRONMENT_URL=$(jq -r '.deploy_url' deploy-output.json)
    //                 npx playwright test  --reporter=html
    //             '''
    //         }

    //         post {
    //             always {
    //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Staging E2E', reportTitles: '', useWrapperFileDirectly: true])
    //             }
    //         }
    //     }

    //     stage('Deploy prod') {
    //         agent {
    //             docker {
    //                 image 'my-playwright'
    //                 reuseNode true
    //             }
    //         }

    //         environment {
    //             CI_ENVIRONMENT_URL = 'YOUR NETLIFY URL'
    //         }

    //         steps {
    //             sh '''
    //                 node --version
    //                 netlify --version
    //                 echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
    //                 netlify status
    //                 netlify deploy --dir=build --prod
    //                 npx playwright test  --reporter=html
    //             '''
    //         }

    //         post {
    //             always {
    //                 publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod E2E', reportTitles: '', useWrapperFileDirectly: true])
    //             }
    //         }
    //     }
     
