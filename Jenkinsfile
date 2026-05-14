pipeline {
    agent any

    parameters {
        choice(name: 'ENV_NAME', choices: ['test', 'pp', 'prod'], description: 'Environnement cible des tests')
    }

    tools {
        nodejs 'node24'
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
                bat """
                newman run collections/api-ci-project-collection.json ^
                -e environments/environments-${ENV_NAME}.json ^
                -r cli,htmlextra ^
                --reporter-htmlextra-export reports\\report.html
                """
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'reports/*.html', fingerprint: true
        }
        success {
            emailext(
                subject: "✅ BUILD OK — ${JOB_NAME} #${BUILD_NUMBER}",
                body: """<h2>Build réussi !</h2>
                         <p>Job : <b>${JOB_NAME}</b></p>
                         <p>Build : <b>#${BUILD_NUMBER}</b></p>
                         <p>Environnement : <b>${ENV_NAME}</b></p>
                         <a href="${BUILD_URL}">Voir le build</a>""",
                mimeType: 'text/html',
                to: 'sabrinalaradi13@gmail.com',
                attachmentsPattern: 'reports/*.html'
            )
        }
        failure {
            emailext(
                subject: "❌ BUILD ÉCHOUÉ — ${JOB_NAME} #${BUILD_NUMBER}",
                body: """<h2>Build en échec !</h2>
                         <p>Job : <b>${JOB_NAME}</b></p>
                         <p>Environnement : <b>${ENV_NAME}</b></p>
                         <a href="${BUILD_URL}console">Voir les logs</a>""",
                mimeType: 'text/html',
                to: 'sabrinalaradi13@gmail.com'
            )
        }
    }
}