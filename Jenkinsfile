pipeline {
    agent any
    options {
        quietPeriod(0)
    }
    environment {
        REPO_NAME = 'optima-control-runtime'
        SUBMODULE_NAME = 'ubiquitous-happiness'
    }
    stages {
        stage('Setup') {
            steps {
                script{
                    env.INTEGRATION_BRANCH = "${REPO_NAME}_${env.BRANCH_NAME}"

                }
            }
        }
        stage('Pre-check') {
            steps {
                echo "Hello, everything looks fine!"
            }
        }
        stage('Prepare integration branch') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    withEnv(["GIT_SSH_COMMAND=ssh -o StrictHostKeyChecking=no -l git -i ${SSH_KEY}"]) {                        
                        sh """
git clone git@github.com:zbtn/reimagined-palm-tree.git integration-workspace
cd "${WORKSPACE}/integration-workspace"
git checkout -b "${env.INTEGRATION_BRANCH}"
echo 1.0.0 > changes.txt
git add .
git commit -m "Update submodules"
git remote -v
git push --force --set-upstream origin "${env.INTEGRATION_BRANCH}"
git log --format=\"%H\" -n 1 > ${WORKSPACE}/last_hash
"""  
                    }
                }
            }
        }
        stage('Trigger integration') {
            steps {
                script {
                    env.TRIGGER_REVISION = readFile "${WORKSPACE}/last_hash"
                }
                build job: 'Integration', parameters: [string(name: 'BRANCH', value: 'integration'), string(name: 'REVISION', value: env.TRIGGER_REVISION)]
            }
        }
    }
}