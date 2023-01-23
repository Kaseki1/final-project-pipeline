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
                    userRemoteConfigs: [[credentialsId: 'final-project-git', url: 'git@github.com:sepulturese/crcs.git']]
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
                                    sourceFiles: "build/*",
                                    //removePrefix: "",
                                    //remoteDirectory: "",
                                    execCommand: "pkill server; sleep 20; chmod +x ./final-project/build/server; ./final-project/build/server > server.log & disown; echo Deployed!; exit 0"
                                )
                        ])
                    ])
                }
            }
        }
    }
}
