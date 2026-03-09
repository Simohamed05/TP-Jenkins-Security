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

        stage('SCA Scan') {
            steps {
                sh '''
                    mkdir -p odc-data dependency-check-report

                    dependency-check.sh \
                      --project "TP-Jenkins" \
                      --scan . \
                      --format HTML \
                      --out dependency-check-report \
                      --data odc-data \
                      --disableRetireJS \
                      --disableKnownExploited
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
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Build failed due to errors or vulnerabilities'
        }
    }
}
