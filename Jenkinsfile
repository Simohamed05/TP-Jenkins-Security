pipeline {
  agent any

  environment {
    VENV = ".venv"
    ODC_DATA = "odc-data"
    ODC_OUT  = "dependency-check-report"
    // si tu ajoutes la clé dans Jenkins credentials -> tu peux l'injecter avec withCredentials
  }

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
          set -e
          mkdir -p "${ODC_DATA}" "${ODC_OUT}"

          command -v dependency-check.sh

          # 1er build: enlève --noupdate pour télécharger la DB
          dependency-check.sh \
            --project "TP-Jenkins" \
            --scan . \
            --format HTML \
            --out "${ODC_OUT}" \
            --data "${ODC_DATA}" \
            --disableRetireJS \
            --disableKnownExploited
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'dependency-check-report/**', fingerprint: true, allowEmptyArchive: true
      junit 'test-results.xml'
    }
  }
}
