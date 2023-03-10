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
        stage('Unit Tests') {
            steps {
                echo 'Test'
            }
        }
        stage('Deploy to stage environment') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "stage-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    sourceFiles: "build/*, install_server.sh"
                                )
                        ])
                    ])
                }
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "stage-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: "chmod +x final-project/install_server.sh; cd final-project; sudo ./install_server.sh"
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
                echo 'Deploy to production pproved'
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
                                    sourceFiles: "build/*, install_server.sh"
                                )
                        ])
                    ])
                }
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "prod-ssh-key",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: "chmod +x final-project/install_server.sh; cd final-project; sudo ./install_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
    }
}
