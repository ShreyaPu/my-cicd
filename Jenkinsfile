pipeline {
    agent any
    tools{
        jdk  'jdk11'
        maven  'maven3'
    }
   
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
   
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/jaiswaladi246/Ekart.git'
                
            }
        }
       
        stage('COMPILE') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
       
       
        stage('Sonarqube Analysis') {
            steps 
               {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.url=http://13.234.119.68:9000/ -Dsonar.login=squ_19152dfd17d08ec8846f5c12d185780a55958c4a -Dsonar.projectName=Ekart \
                   -Dsonar.java.binaries=. \
                   -Dsonar.projectKey=Ekart '''
               }
            
        }
       
        stage('Build Application') {
            steps {
                sh "mvn clean package -DskipTests=true"
            }
        }
       
        stage('Build & Push Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'c33380ca-d873-42fa-9311-5f634ffaf2e6', toolName: 'docker') {
                        sh "docker build -t eekart -f docker/Dockerfile ."
                        sh "docker tag  eekart shreyapundkar/eekart:latest"
                        sh "docker push shreyapundkar/eekart:latest"
                    }
                }
            }
        }
        
        stage('trigger CD pipeline'){
            steps {
                build job: "CD_Pipeline" , wait: true
            }
        }
       
       
    }
}
