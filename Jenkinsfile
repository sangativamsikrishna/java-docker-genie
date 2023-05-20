pipeline {
    agent any
    
    tools{
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/SushantOps/Devops-CICD.git'
            }
        }
        
        stage('CODE COMPILE') {
            steps {
               sh "mvn clean compile"
            }
        }
        
        stage('TEST CASES') {
            steps {
               sh "mvn test"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
               withSonarQubeEnv('sonar-scanner') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Devops-CICD \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=Devops-CICD '''
                }
             }
        }
        stage('TRIVY SCAN') {
            steps {
               sh " trivy fs --security-checks vuln,config /var/lib/jenkins/workspace/CICD "
            }
        }
        
        stage('CODE BUILD') {
            steps {
               sh " mvn clean install "
            }
        }
        
        stage('DOCKER BUILD') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'b0f72c00-5ae5-4b49-ab36-93350a87f2a6', toolName: 'docker-latest') {
                        sh "docker build -t cicddevops ."
                         
                    }
               }
            }
        }
        
        stage('DOCKER PUSH') {
            steps {
               script{
                   withDockerRegistry(credentialsId: 'b0f72c00-5ae5-4b49-ab36-93350a87f2a6', toolName: 'docker-latest') {
                        sh "docker tag cicddevops sushantkapare1717/cicddevops:$BUILD_ID "
                        sh "docker push sushantkapare1717/cicddevops:$BUILD_ID "
                         
                    }
               }
            }
        }
        
    }

}
