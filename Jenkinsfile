pipeline {
    agent any

    stages {

        stage('Clone Repository') {
          steps {
            git branch: 'main', url: 'https://github.com/Simohamed05/TP-Jenkins-Security.git'
          }
        }
                stage('Install Dependencies') {
          steps {
            sh 'pip3 install -r requirements.txt'
          }
        }
        
        stage('Run Tests') {
          steps {
            sh 'pytest -q'
          }
        }

        stage('SCA Scan') {
            steps {
                sh 'dependency-check.sh --project "TP-Jenkins" --scan . --format HTML'
            }
        }

        stage('SAST Scan') {
            steps {
                sh 'sonar-scanner'
            }
        }
    }

    post {
        failure {
            echo 'Build failed due to errors or vulnerabilities'
        }
    }
}
