pipeline {
  agent any
  stages {
    stage('Scan') {
      steps {
        sh 'mkdir -p reports'
        script {
          def failed = []
          if (sh(returnStatus: true, script: 'gitleaks detect --source . --report-format json --report-path reports/gitleaks.json --exit-code 1') != 0) failed << 'secrets'
          if (sh(returnStatus: true, script: 'semgrep scan --config auto --severity ERROR --error --json -o reports/semgrep.json') != 0) failed << 'code-issues'
          if (sh(returnStatus: true, script: 'trivy fs --severity CRITICAL,HIGH --ignore-unfixed --exit-code 1 --format json -o reports/trivy.json .') != 0) failed << 'dependencies'
          archiveArtifacts artifacts: 'reports/**', allowEmptyArchive: true
          if (failed) { error("Scans failed: ${failed.join(', ')}") }
        }
      }
    }
  }
}
