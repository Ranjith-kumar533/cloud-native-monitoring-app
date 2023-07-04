pipeline {
    agent any
    
    environment {
        def user = 'ubuntu'
        def host = '172.31.90.145'
    }

    stages {
        stage('Git checkout') {
            steps {
                script {
                    git branch: 'main', url: 'https://github.com/Ranjith-kumar533/cloud-native-monitoring-app.git'
                }
            }
        }
        stage('Docker image') {
            steps {
                script {
                    def tag = "nativecloudapp:${env.BUILD_NUMBER}"
                    def dockerfile = "~/Downloads/Dockerfile"
                    sshagent(['Dockerprivkey']) {
                        sh "scp -r -o StrictHostkeyChecking=no /var/lib/jenkins/workspace/Native-cloud-app/* ${user}@${host}:~/Downloads/"
                        sh "ssh -o StrictHostKeyChecking=no ${user}@${host} 'docker build -t ${tag} ~/Downloads'"
                    }
                }
            }
        }
    }
}