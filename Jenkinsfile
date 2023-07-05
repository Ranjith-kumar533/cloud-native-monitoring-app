pipeline {
    agent any
    
    environment {
        def user = 'ubuntu'
        def host = '172.31.20.158'
        def tag = "nativecloudapp:${env.BUILD_NUMBER}"
        def image = "ranjik/test:nativecloudappv${env.BUILD_NUMBER}"
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
                    sshagent(['Dockerprivkey']) {
                        sh "scp -r -o StrictHostkeyChecking=no /var/lib/jenkins/workspace/Native-cloud-app/* ${user}@${host}:~/Downloads/"
                        sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker build -t ${tag} ~/Downloads'"
                    }
                }
            }
        }
        stage('Pushing images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'Dockerhubcreds', usernameVariable: 'DockerhubUsername', passwordVariable: "DockerhubPassword")]) {
                    script {
                        sshagent(['Dockerprivkey']) {
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'echo ${DockerhubPassword} | docker login -u ${DockerhubUsername} --password-stdin'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker tag ${tag} ${image}'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker push ${image}'"

                        }
                    }
                }
            }
        }
    }
}
