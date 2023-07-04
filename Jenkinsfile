pipeline {
    agent any
    
    environment {
        def user = 'ubuntu'
        def host = '172.31.17.78'
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
                    def tag = "${BUILD_NUMBER}"
                    sshagent(['Dockerprivkey']) {
                        sh "scp -o StrictHostkeyChecking=no /var/lib/jenkins/workspace/'Native cloud app'/Dockerfile ${user}@${host}:~/Downloads/"
                        sh "ssh -o StrictHostKeyChecking=no ${user}@${host} docker build -t cloudapp:${tag} -f ~/Downloads/Dockerfile ~/Downloads"
                    }
                }
            }
        }
    }
}
