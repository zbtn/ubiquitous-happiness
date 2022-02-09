pipeline {
    agent any
    stages {
        stage('Test') {
            steps {
                echo "Hello from submodule"
                build job: 'Integration'
            }
        }
    }
}