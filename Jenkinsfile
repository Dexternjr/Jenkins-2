pipeline {
    agent any
    
    parameters {
        string(name: 'username', defaultValue: '', description: 'Username for remote server')
        string(name: 'server_dns', defaultValue: '', description: 'DNS of the remote server')
        string(name: 'password', defaultValue: '', description: 'Password for remote server')
        string(name: 'branch_name', defaultValue: 'main', description: 'Branch name for GitHub')
        booleanParam(name: 'delete_application', defaultValue: false, description: 'Delete application after deployment')
    }
    
    stages {
        stage('Clean Jenkins Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Clone from GitHub') {
            when {
                expression {
                    !params.delete_application
                }
            }
            steps {
                script {
                    git branch: "${params.branch_name}", url: 'https://github.com/chrisdylan237/Demo1.git'
                }
            }
        }
        
        stage('Copy files to remote server') {
            when {
                expression {
                    !params.delete_application
                }
            }
            steps {
                script {
                    sh "sshpass -p '${params.password}' scp -o StrictHostKeyChecking=no -r * ${params.username}@${params.server_dns}:."
                }
            }
        }
        
        stage('Check files on remote server') {
            when {
                expression {
                    !params.delete_application
                }
            }
            steps {
                script {
                    sh "sshpass -p '${params.password}' ssh -o StrictHostKeyChecking=no ${params.username}@${params.server_dns} 'ls'"
                }
            }
        }
        
        stage('Deploy application on remote server') {
            when {
                expression {
                    !params.delete_application
                }
            }
            steps {
                script {
                    sh "sshpass -p '${params.password}' ssh -o StrictHostKeyChecking=no ${params.username}@${params.server_dns} 'sudo docker-compose up -d'"
                }
            }
        }
        
        stage('Delete application') {
            when {
                expression {
                    params.delete_application == true
                }
            }
            steps {
                script {
                    sh "sshpass -p '${params.password}' ssh -o StrictHostKeyChecking=no ${params.username}@${params.server_dns} 'sudo docker-compose down --rmi all --volumes --remove-orphans'"
                }
            }
        }
    }
}
