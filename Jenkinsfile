pipeline {
    agent any
    options {
        quietPeriod(0)
    }
    environment {
        REPO_NAME = 'optima-control-runtime'
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
                withCredentials([sshUserPrivateKey(credentialsId: 'github-ssh-key', keyFileVariable: 'SSH_KEY', usernameVariable: 'SSH_USER')]) {
                    withEnv(["GIT_SSH_COMMAND=ssh -o StrictHostKeyChecking=no -l git -i ${SSH_KEY}"]) {                        
                        sh """
git clone git@github.com:zbtn/reimagined-palm-tree.git integration-workspace
cd "${WORKSPACE}/integration-workspace"
git checkout -b "${REPO_NAME}_${env.BRANCH_NAME}"
echo 1.0.0 > changes.txt
git add .
git commit -m "Update modules"
git remote -v
git push --set-upstream origin ${env.BRANCH_NAME}
mkdir -p ${WORKSPACE_TMP}
git log --format=\"%H\" -n 1 > ${WORKSPACE_TMP}/last_hash
"""  
                    }
                }
            }
        }
        stage('Trigger integration') {
            steps {
                script {
                    env.TRIGGER_REVISION = readFile '${WORKSPACE_TMP}/last_hash'
                }
                build job: 'Integration', parameters: [string(name: 'BRANCH', value: env.TRIGGER_REVISION)]
            }
        }
    }
}