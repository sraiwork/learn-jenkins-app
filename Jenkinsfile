pipeline {
    agent any

    stages {
        //npm build command execution
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
                    npm --version
                    node --version
                    npm ci
                    npm run build
                '''
            }
        }
        /*
            npm command not found for this stage since docker destroyed in previous step.
            hence creattind docker agent again.
        */
        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }            
            steps {
                sh '''
                    echo "Test Stage"
                    test -f build/index.html
                    npm test
                '''
            }
        }
        //Playwright End-to-End testing.
        stage('E2E') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.58.0-noble'
                    reuseNode true
                    //args '-u rrot:root'
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
            junit 'test-results/junit.xml'
        }
    }
}
