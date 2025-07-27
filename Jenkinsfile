pipeline {
    agent any

    stages {
        stage('Clone Repos') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [
                        [$class: 'SubmoduleOption', recursiveSubmodules: true, trackingSubmodules: true]
                    ],
                    userRemoteConfigs: [[
                        url: 'https://github.com/pratyushsingha/clikit.git'
                    ]]
                ])
            }
        }

        stage('Docker Compose Build && Push') {
            steps {
                echo 'Building the Docker Image...'
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerHubCred',
                        passwordVariable: 'dockerHubPass',
                        usernameVariable: 'dockerHubUser'
                    )
                ]) {
                    sh 'docker login -u $dockerHubUser -p $dockerHubPass'
                    sh 'docker compose build'
                    echo 'Pushing Docker Images to Docker Hub...'
                    sh 'docker push pratyush044/clikit_client:latest'
                    sh 'docker push pratyush044/clikit_server:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy on ec2...'
                dir('/var/lib/jenkins/workspace/Threads') {
                    sh 'docker compose down --remove-orphans'
                    sh 'docker compose pull'
                    sh 'docker compose up -d --remove-orphans'
                }
            }
        }
    }
}