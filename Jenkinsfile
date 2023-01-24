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
            steps {
                sh '(cd server && make)'
                sh 'test -d build || mkdir build'
                sh 'cp server/debug/server build/'
            }
        }
        stage('Test') {
            steps {
                echo 'Test'
            }
        }
        stage('Transfer') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "final-project",
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
        stage('Deploy') {
            steps {
                script {
                    sshPublisher(
                        continueOnError: false, failOnError: true,
                        publishers: [
                            sshPublisherDesc(
                            configName: "final-project",
                            verbose: true,
                            transfers: [
                                sshTransfer(
                                    execCommand: "sudo chmod +x final-project/deploy_server.sh; ./final-project/deploy_server.sh"
                                )
                        ])
                    ])
                }
            }
        }
    }
}
