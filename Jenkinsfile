pipeline {
    agent any

    stages {
        stage('Build') {
            agent{
                docker{
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
        stage ('Test'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                test -f build/index.html
                npm test
                '''
            }
             post{
                always{
                    junit 'test-results/junit.xml'
                }
            }
        }

        stage('E2E'){
            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s buils &
                    sleep 10
                    npx playwright test --reporter=html
                 '''
            }
            post {
                always{
                    publishHTML([allowMissing:false,alwaysLinkToLastBuild:false,keepAll:false,reportDir:'playwright-report',reportFiles:'index.html',reportName:'Playwright HTML report',reportTitles:'',useWrapperFileDirectory:true])
                }
            }
        }

        stage('Deploy')
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
               sh '''
                    npm install -g netlify-cli
                    netlify --version
               '''
            }
        }
    }
   
}
