pipeline {
    agent any
    
    tools {
        nodejs 'nodejs'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git 'https://github.com/arifsadiq/node-comingsoon-app.git'
            }
        }
        stage('NPM Dependency Install') {
            steps {
                sh 'npm install'
                sh 'npm audit'
            }
        }
        stage('NPM Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('SonarQube Scanner') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=nodeproj \
                      -Dsonar.projectKey=nodeproj -Dsonar.java.binaries=. '''

                }
            }
        }
        stage('Docker Build') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t nodeapp:latest .'
                    }
                }
            }
        }
        stage('Docker Tag and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker tag nodeapp ari786/nodeapp:latest'
                        sh 'docker push ari786/nodeapp:latest'
                    }
                }
            }
        }
        stage('Docker Run') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker run -d -p 3000:3000 ari786/nodeapp:latest'
                    }
                }
            }
        }
    }
}
