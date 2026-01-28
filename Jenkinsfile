pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Generate Coverage Report') {
            steps {
                sh 'npm run coverage'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                sh '''
                #!/bin/bash
                wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-5.14.0.42296-macosx.zip -O sonar-scanner.zip
                unzip -o sonar-scanner.zip
                ./sonar-scanner-5.14.0.42296-macosx/bin/sonar-scanner \
                  -Dsonar.projectKey=<your_project_key> \
                  -Dsonar.organization=<your_organization_name> \
                  -Dsonar.host.url=https://sonarcloud.io \
                  -Dsonar.login=$SONAR_TOKEN
                '''
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                archiveArtifacts artifacts: 'dist/**', fingerprint: true
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished. Check logs for errors.'
        }
    }
}
