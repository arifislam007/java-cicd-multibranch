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
                git branch: 'main', url: 'https://github.com/arifislam007/java-cicd-multibranch.git'
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
        stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "java-cicd-multibranch"
            GIT_USER_NAME = "arifislam007"
        }
        stage('Clone another git branch') {
            steps {
                withCredentials([string(credentialsId: 'gitaccess', variable: 'GITHUB_TOKEN')]){
                    sh '''
                        git config user.email 'islam.arif87@gmail.com'
                        git config user.name 'arifislam007'
                        git add .
                        git commit -m 'update'
                        git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:staging
                    '''
                }
            }
        }
        
    }
}
