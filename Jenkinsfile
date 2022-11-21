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
        stage('Scan for secrets') {
            steps {
                sh '''
                    curl -LO https://github.com/zricethezav/gitleaks/releases/download/v8.9.0/gitleaks_8.9.0_linux_x64.tar.gz
                    tar -xzf gitleaks_8.9.0_linux_x64.tar.gz
                    ./gitleaks protect -v // Scan for commonly leaked secrets
                    rm -rf gitleaks*
                '''
            }
        }
    }
}
