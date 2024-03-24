pipeline {
    agent any
    
    tools {
        jdk 'jdk'
        maven 'mvn'
    }
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/arifislam007/cicd-project-1.git'
            }
        }
        stage('Code Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test Stage') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build Package') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Docker Build') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t arifislam/my-cicd:${BUILD_NUMBER} ."
                    }
                }
            }
        }
        stage('Update Deployment File Image Tag') {
            steps {
                sh "sed -i 's/spring-bot:base/my-cicd:${BUILD_NUMBER}/' ./deployment.yml"
            }
        }
        stage('Clone another git branch') {
            steps {
                dir('test') {
                    git branch: 'staging', credentialsId: 'gitaccess', url: 'https://github.com/arifislam007/cicd-project-1.git'
                    sh "git config user.email 'islam.arif87@gmail.com'"
                    sh "git config user.name 'arifislam007'"
                    sh "cp -f ../deployment.yml ./test/deployment"
                    sh "git add ."
                    sh "git commit -m 'update'"
                    git push https://github.com/arifislam007/cicd-project-1.git HEAD:staging
                }
            }
        }
        
    }

