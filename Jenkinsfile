pipeline {
    agent any

    stages {
        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                script {
                    bat 'vagrant validate'  // Run on Windows
                }
            }
        }

        // Deployment Stage
        stage('Deployment') {
            steps {
                echo 'Starting Vagrant VMs...'
                script {
                    bat 'vagrant up'  // Run on Windows
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed.'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed. See logs for details.'
            script {
                bat 'vagrant destroy -f'
            }
        }
    }
}