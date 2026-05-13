pipeline {
    agent any

    tools {
        nodejs 'node18'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Newman') {
            steps {
                bat 'npm install -g newman'
                bat 'npm install -g newman-reporter-htmlextra'
            }
        }

        stage('Run API Tests') {
            steps {
                bat '''
                newman run collections/api-ci-project-collection.json ^
                -e environments/environments-test.json ^
                -r cli,htmlextra ^
                --reporter-htmlextra-export reports\\report.html
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html', fingerprint: true
        }
    }
}