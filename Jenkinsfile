#!/usr/bin.env groovy

library identifier: 'jenkins-shared@main', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/Nicoleta-Sandu/jenkins-shared.git',
    credentialsID: 'github-credentials'
    ]
)

pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        IMAGE_NAME = 'nykolesandu/example:1.0'
    }
    stages {
        stage('build app') {
            steps {
                echo 'building application jar...'
                buildJar()
            }
        }
        stage('build image') {
            steps {
                script {
                    echo 'building the docker image...'
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        } 
        stage("deploy") {
            steps {
                script {
                    def dockerCmd = 'docker run -d -p 8080:8080 nykolesandu/example:1.0'
                    sshagent(['ec2-server-key']){
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@52.57.70.6 ${dockerCmd}"
                    }
                }
            }
        }               
    }
} 
