pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

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
          pytest -q --junitxml=test-results.xml
        '''
      }
    }

    stage('SCA Scan (OWASP Dependency-Check)') {
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
            --disableAssembly
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dependency-check-report/**', fingerprint: true
      junit 'test-results.xml'
    }
  }
}
