# devops-automation


![Screenshot (7)](https://github.com/pillakarthik4/DevOps-Automation/assets/130967802/93f96318-117e-4093-81df-f3296ca853e3)

#Added Sonarqube for static analysis of code

![Screenshot (9)](https://github.com/pillakarthik4/DevOps-Automation/assets/130967802/30a68f24-b03a-4cc5-8d33-02590d90536d)

#Pipe line Success

#Dashboard of Sonarqube is below:

![Screenshot (8)](https://github.com/pillakarthik4/DevOps-Automation/assets/130967802/52034f58-f6e2-4f07-bcc4-6f4b4c50f93a)

## JENKINS PIPELINE FOR THIS PROJECT 
----------------------------------------
pipeline {
    agent any
    tools {
        maven 'maven'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/pillakarthik4/DevOps-Automation.git']]])
                sh 'mvn clean install'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    def mvn = tool 'maven'
                    withSonarQubeEnv() {
                        sh "${mvn}/bin/mvn clean verify sonar:sonar -Dsonar.projectKey=DevOps--automation1"
                    }
                }
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    sh 'docker build -t karthikpilla/kubernetes .'
                }
            }
        }
        stage('Push image to hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                        sh 'docker login -u karthikpilla -p ${dockerhubpwd}'
                    }
                    sh 'docker push karthikpilla/kubernetes'
                }
            }
        }
    }
}



