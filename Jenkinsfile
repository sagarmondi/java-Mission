pipeline {
  agent any
  environment {
    // Required for Semgrep AppSec Platform-connected scan
    SEMGREP_APP_TOKEN = credentials('SEMGREP_APP_TOKEN')
  }
  stages {
    stage('Install Semgrep') {
      steps {
        // Install Semgrep tool
        sh 'pip3 install semgrep'
      }
    }
    
    stage('Retrieve Rules') {
      steps {
        script {
          // Retrieve rules from Semgrep registry (this command lists the rules and saves them in rules.json)
          sh '/var/lib/jenkins/.local/bin/semgrep --config=auto --json > rules.json'
        }
      }
    }

    stage('Run Semgrep Scan') {
      steps {
        script {
          // Run the Semgrep scan in CI mode, which includes both code and supply chain scans
          sh '/var/lib/jenkins/.local/bin/semgrep ci --json --no-suppress-errors -o results.json'
        }
      }
    }

    stage('Combine Results and Rules') {
      steps {
        script {
          // Use jq or a similar tool to merge rules.json and results.json
          // This command assumes jq is installed on your Jenkins instance
          sh 'jq -s \'{"rules": .[0], "scan_results": .[1]}\' rules.json results.json > combined_results.json'
        }
      }
    }

    stage('Archive Results') {
      steps {
        // Archive the combined_results.json file as an artifact in Jenkins
        archiveArtifacts artifacts: 'combined_results.json', allowEmptyArchive: true
      }
    }
  }
}
