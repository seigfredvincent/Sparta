pipeline {
    agent any

    stages {
        // Security Check Stage
        stage('Security Check') {
            steps {
                echo 'Running Vagrant Validation...'
                script {
                    bat 'vagrant validate'
                }
            }
        }

        // Deployment Stage
        stage('Deploy VMs') {
            steps {
                echo 'Starting Vagrant VMs...'
                script {
                    bat 'vagrant up' 
                }
            }
        }

        // Testing Web Server
        stage('Testing Web Server') {
            steps {
                echo 'Testing Web Server Connectivity and Apache Status...'
                script {
                    bat '''vagrant ssh web_server -c "curl -I http://localhost | grep HTTP/1.1"'''
                }
            }
        }

        // Testing Database Server
        stage('Testing Database Server') {
            steps {
                // echo 'Testing Database Server Connectivity and MySQL Status...'
                // script {
                //     bat '''vagrant ssh db_server -c "systemctl status mysql | grep Active"'''
                // }
                withCredentials([[$class: 'VaultUsernamePasswordCredentialBinding', credentialsId: 'vault-jenkins', passwordVariable: 'DB_PASSWORD', usernameVariable: 'DB_USERNAME']]) {
                    // Use the Vault credentials for testing connection
                    sh """
                    vagrant ssh web_server -c "mysql -h 192.168.57.81 -u $DB_USERNAME -p$DB_PASSWORD -e 'SHOW DATABASES;'"
                    """
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