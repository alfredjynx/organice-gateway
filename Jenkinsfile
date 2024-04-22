pipeline {
    agent any
    stages {
        stage('Build Discovery') {
            steps {
                build job: 'organice-discovery', wait: true
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