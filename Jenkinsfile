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
                      --disableKnownExploited \
                      --disableAssembly \
                      --failOnCVSS 7
                '''
            }
        }

        stage('SAST Scan') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                              -Dsonar.projectKey=TP-Jenkins-Security \
                              -Dsonar.projectName=TP-Jenkins-Security \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=http://host.docker.internal:9000
                        """
                    }
                }
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
