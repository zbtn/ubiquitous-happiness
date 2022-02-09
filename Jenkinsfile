pipeline {
    agent any
    stages {
        stage('Setup') {
            steps {
                echo "Preparing build"
            }
        }
        stage('Pre-check') {
            steps {
                echo "Hello, everything looks fine!"
            }
        }
        stage('Prepare integration branch') {
            steps {
                checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '**']], extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: '']], userRemoteConfigs: [[credentialsId: 'github-ssh-key', name: 'workspace', url: 'git@github.com:zbtn/reimagined-palm-tree.git']]]
                dir('integration/workspace') {
                    withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                       sh """
git checkout -b integration
echo 1.0.0 > changes.txt
git config --global user.email m.zbytniewski@microsolutions.pl
git config --global user.name Jenkins
git push --set-upstream origin intergation
"""  
                    }
                }
            }
        }
        stage('Trigger integration') {
            steps {
                build job: 'Integration', parameters: [string(name: 'BRANCH', value: '')]
            }
        }
    }
}