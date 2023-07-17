pipeline {
    agent any
    parameters{
        string(name: 'Enter_app_name', defaultValue:'', description: '')
    }
     environment {
                //def user = 'ubuntu'
                //def host = '172.31.20.158'
                def appname = "${params.Enter_app_name}"
                def tag = "${appname}v${BUILD_NUMBER}"
                def image = "ranjik/testrepository:${appname}v${BUILD_NUMBER}"
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
                   /* sshagent(['Dockerprivkey']) {
                        sh "scp -r -o StrictHostkeyChecking=no /var/lib/jenkins/workspace/Native-cloud-app/* ${user}@${host}:~/Downloads/"
                        sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker build -t ${tag} ~/Downloads'"
                    }*/
                    sh 'docker build -t ${tag} .'
                }
            }
        }
        stage('Pushing images') {
            
            steps {
                 script {
                        /*sshagent(['Dockerprivkey']) {
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'echo ${DockerhubPassword} | docker login -u ${DockerhubUsername} --password-stdin'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker tag ${tag} ${image}'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker push ${image}'"

                        }*/
                        // This step should not normally be used in your script. Consult the inline help for details.
                         sh 'docker tag ${tag} ${image}'
                         def dockerImage=docker.image("${image}")
                         withDockerRegistry(credentialsId: 'Dockerhubcreds', url: 'https://index.docker.io/v1/') {
                         dockerImage.push()
                        }
                    }
                
            }
        }
        stage('Update Deployment File') {
        environment {
            GIT_REPO_NAME = "cloud-native-monitoring-app"
            GIT_USER_NAME = "Ranjith-kumar533"
        }
        steps {
            withCredentials([string(credentialsId: 'Github-Token', variable: 'GITHUB_TOKEN')]) {
                sh """
                    git config user.email "ranjith@gmail.com"
                    git config user.name "ranjith"
                    sed -i 's@image: .*@image: ${image}@; s@name: .*@name: ${tag}@' ${WORKSPACE}/Deployments/deploy.yaml
                    git add ${WORKSPACE}/Deployments/deploy.yaml
                    git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                    git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME} HEAD:main
                """
            }
        }
    }
    }
    post{
        success{
            script{
            sh "docker rmi -f \$(docker images | grep ${BUILD_NUMBER})"
            }
        }
    }
}
