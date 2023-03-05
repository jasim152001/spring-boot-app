pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS=credentials('dockerhub')
    }

    stages {
        stage('git-checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shefink/spring-boot-app.git'
            }
        }
        stage('maven-build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('sonar-test') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn clean verify sonar:sonar' 
                }
            }
        }
        stage('Delete-image') {
            steps{
                sh 'sudo docker rmi shefink/spring-boot-app:latest'
            }
        }
        stage('docker-image-build') {
            steps {
                sh 'sudo docker build -t shefink/spring-boot-app .'
            }
        }
        stage('login-to-dokcer-hub') {
            steps {
                sh 'sudo echo $DOCKERHUB_CREDENTIALS_PSW | sudo docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push-docker-hub') {
            steps {
                sh 'sudo docker push shefink/spring-boot-app'
            }
        }
        stage('k8s-deployment') {
            steps {
                sh 'sudo kubectl replace --force -f Deployment_file.yml'
            }
        }
    }
        
}

