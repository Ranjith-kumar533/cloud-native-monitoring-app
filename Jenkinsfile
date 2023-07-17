pipeline {
    agent any
    parameters{
        // To parameterize the pipeline for app name.
        string(name: 'Enter_app_name', defaultValue:'', description: '')
    }
     environment {
                // we can mention the username and host ip/name as an evironment variables if we are using multiple servers.
                //def user = 'ubuntu'
                //def host = 'host-ip/name'
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
                    // This commented block will be useful when we want to execute commands in the remote shell. This can be achieved by using SSHagent plugin in jenkins.
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
                      // This commented block will be useful when we want to execute commands in the remote shell. This can be achieved by using SSHagent plugin in jenkins.
                        /*sshagent(['Dockerprivkey']) {
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'echo ${DockerhubPassword} | docker login -u ${DockerhubUsername} --password-stdin'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker tag ${tag} ${image}'"
                            sh "ssh -o StrictHostkeyChecking=no ${user}@${host} 'docker push ${image}'"

                        }*/
                        // Using Docker plugin feature to push the image to repository.
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
            //Shell Script to update the app name and image name in the deploy.yaml file
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
    // Post action for the pipeline
    post{
        success{
            script{
            // Since we are pushing the images to the docker hub, we dont want the images to be in the server, so we remove the images once the deployment is successfull.
            sh "docker rmi -f \$(docker images | grep ${BUILD_NUMBER})"
            }
        }
    }
}
