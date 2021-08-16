pipeline {
    agent any
    
  environment {
    registry="ankit310/nagp-biannual"
  }
  tools{
    maven 'Maven3'
  }
  
  options{
    timestamps()
    timeout(time:1, unit: 'HOURS')
    buildDiscarder(logRotator(daysToKeepStr:'10', numToKeepStr: '20'))
  }
  
    stages {
        stage('Checkout') {
            steps {
                echo 'Performing Checkout'
                checkout scm
            }
        }
      
        stage('Build') {
            steps {
                echo 'Performing Build'
                bat "mvn clean install"
            }
        }
      
        stage('Test cases') {
            steps {
                echo 'Performing Test cases'
                bat "mvn test"
            }
        }
      
        stage('SonarQube') {
            steps {
              withSonarQubeEnv("Test_Sonar"){
                bat "mvn sonar:sonar"
              }
            }
        }
        
        stage('Build docker') {
            steps {
                echo 'Performing Build docker'
                bat "docker build -t i-ankit310-feature:${BUILD_NUMBER} --no-cache -f Dockerfile ."
            }
        }
        
        stage('Push Docker') {
            parallel {
                stage('Precontainer Check'){
                    steps{
                        bat "docker rm -f c-ankit310-feature || exit 0 && docker rm c-ankit310-feature || exit"
                    }
                }
                
                stage('Push to Docker'){
                    steps{
                        bat "docker tag i-ankit310-feature:${BUILD_NUMBER} ${registry}:feature-${BUILD_NUMBER}"
                        bat "docker tag i-ankit310-feature:${BUILD_NUMBER} ${registry}:feature-latest"
                        withDockerRegistry([credentialsId: 'Test_Docker', url: ""]){
                            bat "docker push ${registry}:feature-${BUILD_NUMBER}"
                            bat "docker push ${registry}:feature-latest"
                        }
                    }
                }   
            }
        }
        
        stage('Docker deployment'){
            steps{
                bat "docker run --name c-ankit310-feature -d -p 7200:8080 ${registry}:feature-latest"
            }
        }
        
        
    }
}
