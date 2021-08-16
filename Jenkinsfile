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
              withSonarQubeEnv("TestSOnar"){
                bat "mvn sonar:sonar"
              }
            }
        }
    }
}
