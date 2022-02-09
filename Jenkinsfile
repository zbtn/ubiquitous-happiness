pipeline {
    agent any
    options {
        quietPeriod(0)
    }
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
                    withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                        withEnv(["GIT_SSH_COMMAND=ssh -o StrictHostKeyChecking=no -l git -i ${SSH_KEY}"]) {                        
                            sh """
printenv | sort
cd 'integration/workspace'
git checkout -b ${env.BRANCH_NAME}
echo 1.0.0 > changes.txt
git add .
git commit -m "Update modules"
git remote -v
git push --set-upstream origin ${env.BRANCH_NAME}
echo "No kurwa mać"
"""  
                        }
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