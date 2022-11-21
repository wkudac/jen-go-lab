pipeline {
    agent any
    tools {
        go 'go-1.19' // Comes from the jenkins global config
    }
    environment {
        ENV = "${env.BRANCH_NAME == 'master' ? 'PROD' : 'DEV'}" // Define the ENV based on the branch name
        BRANCH = "${env.BRANCH_NAME}" // Needed by the deployment script
    }

    stages {
        stage('Build') {
            steps {
                sh 'bash scripts/build.sh' // Run the build
            }
        }
        stage('Test') {
            steps {
                sh 'bash scripts/test.sh' // Run the test
            }
        }
        stage('Deploy') {
            when {
                anyOf {
                    branch 'master';
                    branch 'develop'
                }
            }
            steps {
                sh 'export JENKINS_NODE_COOKIE=do_not_kill ; bash scripts/deploy.sh'
            }
        }
    }
}
