pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/user/project.git'
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
