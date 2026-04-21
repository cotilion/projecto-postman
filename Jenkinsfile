pipeline {
    agent any
    
    parameters {
        choice(
            name: 'TEST_SUITE',
            choices: ['All', 'Posts', 'Comments', 'Users', 'Todos', 'Albums', 'Photos'],
            description: '¿Qué suite de pruebas quieres ejecutar?'
        )
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Newman') {
            steps {
                script {
                    def newmanInstalled = bat(script: 'npm -v newman', returnStatus: true) == 0
                    if (!newmanInstalled) {
                    bat 'npm install -g newman'
                    } else {
                    echo "Newman ya está instalado, saltando instalación"
                    }
                }
            }
        }
        
        stage('Run Postman Tests') {
            steps {
                script {
                    def baseCommand = "newman run JSONPlaceholder.postman_collection.json -e environment-de-jsonplaceholder.json -r htmlextra --reporter-htmlextra-export newman-report.html"
                    
                    if (params.TEST_SUITE == 'All') {
                        bat baseCommand
                    } else {
                        bat baseCommand + " --folder '${params.TEST_SUITE}'"
                    }
                }
            }
        }
        
        stage('Publish Report') {
            steps {
                publishHTML([
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: false,
                    reportDir: '.',
                    reportFiles: "newman-report-${params.TEST_SUITE}.html",
                    reportName: "Postman API Test Report de ${params.TEST_SUITE}"
                ])
            }
        }
    }
    
    post {
        always {
            echo "Los tests han finalizado. Suite ejecutada: ${params.TEST_SUITE}"
        }
    }
}