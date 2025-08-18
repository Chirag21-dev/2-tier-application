pipeline {
    agent any
    
    tools {
        nodejs 'Nodejs23'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Chirag21-dev/2-tier-application.git'
            }
        }
        stage('Frontend Compilation') {
            steps {
                dir('client') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
        
        stage('Backend Compilation') {
            steps {
                dir('api') {
                    sh 'find . -name "*.js" -exec node --check {} +'
                }
            }
        }
       /* 
       stage('GitLeaks Scan') {
            steps {
               sh 'gitleaks detect --source ./client --exit-code 1'
               sh 'gitleaks detect --source ./api --exit-code 1'
            }
        } 
        */
    
         stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs --format table -o fs-report.html .'
            }
        }
        
        stage('Build-Tag & Push Backend Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        dir('api') {
                            sh 'docker build -t chiragks1/backend:latest .'
                            sh 'trivy image --format table -o backend-image-report.html chiragks1/backend:latest '
                            sh 'docker push chiragks1/backend:latest'
                           
                        }
                    }
                }
            }  
        }
    }
}
