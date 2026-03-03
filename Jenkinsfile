pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
          steps {
            sh '''
              python3 -m venv .venv
              . .venv/bin/activate
              pip install --upgrade pip
              pip install -r requirements.txt
            '''
          }
        }
        
        stage('Run Tests') {
          steps {
            sh '''
              . .venv/bin/activate
              pytest -q
            '''
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
