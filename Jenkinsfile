pipeline {
    agent any 
    stages {
        stage('Checkout') {
            steps {
                script {
                    // The below will clone your repo and will be checked out to master branch by default.
                    git credentialsId: 'final-project-git', url: 'https://github.com/sepulturese/crcs.git'
                    // Do a ls -lart to view all the files are cloned. It will be clonned. This is just for you to be sure about it.
                    sh "ls -lart ./*" 
                    // List all branches in your repo. 
                    sh "git branch -a"
                    // Checkout to a specific branch in your repo.
                    sh "git checkout branchname"
                    // List directory
                    sh "ls -la"
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Build'
            }
        }
        stage('Test') {
            steps {
                echo 'Test'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy'
            }
        }
    }
}
