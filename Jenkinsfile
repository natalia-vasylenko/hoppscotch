
pipeline {
    agent any

    options {
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Repository checked out successfully'
            }
        }

        stage('Environment') {
            steps {
                sh '''
                    node --version
                    corepack --version
                    corepack pnpm --version
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    corepack pnpm install --frozen-lockfile
                '''
            }
        }

        stage('Lint') {
            steps {
                echo 'Running Hoppscotch lint checks'
        
                sh '''
                    corepack pnpm lint
                '''
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    test -f package.json
                    test -f pnpm-lock.yaml
                    echo "Repository validation passed"
                '''
            }
        }
    }

    post {
        success {
            echo 'CI Pipeline completed successfully!'
        }
        failure {
            echo 'CI Pipeline failed. Check console output.'
        }
    }
}
