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
                dir('workspace') {
                    checkout changelog: false, poll: false, scm: [$class: 'GitSCM', branches: [[name: '*/none']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-ssh-key', name: 'workspace', url: 'git@github.com:zbtn/reimagined-palm-tree.git']]]
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