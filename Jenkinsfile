pipeline {
    agent any
    stages {
        stage('Build Auth') {
            steps {
                build job: 'organice-auth', wait: true
            }
        }
        stage('Jenkins Gateway') {
            steps {
                echo 'Jenkins Gateway'
            }
        }
        stage('Build') { 
            steps {
                sh 'mvn clean package'
            }
        }      
    }
}