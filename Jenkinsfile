pipeline {
    agent any 
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM',
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [[$class: 'CloneOption', noTags: true, reference: '', shallow: true]],
                    submoduleCfg: [],
                    userRemoteConfigs: [[credentialsId: 'final-project-git', url: 'git@github.com:Kaseki1/crcs.git']]
                ]);
                }
            }
        }
        stage('Build') {
            agent {
                docker {
                    image 'build-container:1'
                    reuseNode true
                }
            }
            steps {
                sh '(cd server && make clean && make)'
                sh 'test -d build || mkdir build'
                sh 'cp server/debug/server build/'
            }
        }
        stage('Test') {
            steps {
                echo 'Test'
            }
        }
        stage('Transfer to development env') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "dev-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: "build/*, deploy_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
        stage('Deploy to development env') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "dev-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: "chmod +x final-project/deploy_server.sh; cd final-project; sudo ./deploy_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
        stage('Deploy to production approval') {
            input {
                message "Do you want to proceed for production deployment?"
            }
            steps {
                echo 'Approved'
            }
        }
        stage('Transfer to production env') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "prod-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: "build/*, deploy_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
        stage('Deploy to production env') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "prod-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: "chmod +x final-project/deploy_server.sh; cd final-project; sudo ./deploy_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
    }
}
