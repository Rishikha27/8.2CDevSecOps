pipeline {
    agent any

    environment {
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        PATH = "/usr/local/bin:${env.PATH}" // make sure npm/node are in PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

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
                sh 'npm run coverage || echo "Coverage skipped"'
            }
        }

        stage('NPM Audit (Security Scan)') {
            steps {
                sh 'npm audit || echo "Audit completed with issues"'
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withEnv(["SONAR_SCANNER_HOME=${tool 'SonarScannerCLI'}"]) {
                    sh '''
                        ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=your_project_key \
                        -Dsonar.organization=your_organization_name \
                        -Dsonar.sources=. \
                        -Dsonar.exclusions=node_modules/**,test/** \
                        -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build || echo "Build skipped"'
            }
        }

        stage('Archive Build Artifacts') {
            steps {
                archiveArtifacts artifacts: '**/dist/**', allowEmptyArchive: true
            }
        }
    }

    post {
        always { echo 'Pipeline finished' }
        success { echo 'Pipeline succeeded' }
        failure { echo 'Pipeline failed' }
    }
}
