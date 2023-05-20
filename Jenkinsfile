pipeline {
    agent any
    
    tools{
        jdk 'jdk'
        maven 'maven3.8'
    }
    
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sangativamsikrishna/java-docker-genie.git'
            }
        }
        stage('Code Compile') {
            steps {
                sh "mvn compile"
            }
        }
        stage('Code Test') {
            steps {
                sh "mvn test"
            }
        }
       
        stage('OWASP Check') {
            steps {
                dependencyCheck additionalArguments: '  --scan ./  ', odcInstallation: 'DB'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
         stage('Code build') {
            steps {
                sh "mvn clean install"
            }
        }
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '5bf9749f-9e3a-4869-a35a-97e00e35eca6') {
                        
                    sh "docker build -t cicd ."
                    sh "docker tag cicd sangativamsikrishna/cicd:latest"
                    sh "docker push sangativamsikrishna/cicd:latest"
                }
                }
            }
        }
        
    }
}
